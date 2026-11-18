# Synchronisation Worker Runbook

| **Last updated** | 2026-10-14 |
| **Scope** | Synchronisation-worker health, diagnostics, recovery controls, and rollback |

Awesonix Mail runs two processes that are supervised independently: the API and the synchronisation worker. The synchronisation worker brings the mailboxes in line with the mail providers.

- API liveness does not mean that the worker is ready.
- A worker outage must not make retained local mail unreadable.

For backup, restore, and upgrades, read the [operations guide](README.md).

In this runbook:

1. [Check health](#check-health)
2. [Read mailbox progress](#read-mailbox-progress)
3. [Startup and shutdown](#startup-and-shutdown)
4. [Account controls](#account-controls)
5. [Generation-fenced derived reset](#generation-fenced-derived-reset)
6. [Failure guide](#failure-guide)
7. [Auto-bin recovery](#auto-bin-recovery)

## Check health

Check the API and the worker separately. Port `8180` belongs to the API, and port `8183` belongs to the worker.

```text
GET http://127.0.0.1:8180/health/live
GET http://127.0.0.1:8180/health/ready
GET http://127.0.0.1:8183/health/live
GET http://127.0.0.1:8183/health/ready
GET /api/sync/diagnostics
GET /api/admin/sync/diagnostics
```

### Diagnostic routes

The authenticated diagnostic is versioned and content-free.

- `/api/sync/diagnostics` reports the accounts of the signed-in owner.
- `/api/admin/sync/diagnostics` is for administrators only. It reports more data, as listed below.

The administrator route also reports these items:

- the fixed conditions of the freshness, content, and history lanes
- the freshness generation and its timestamp
- normalised convergence
- queue counts
- mailbox display data with the owner of each mailbox

Retained provider metrics and exported snapshots use opaque provider identities. They never include these items:

- credentials
- subjects, bodies, or attachment names
- message identifiers
- storage paths or content keys
- private folder names
- raw provider error text

### The Synchronisation page

**Administration > Synchronisation** is the primary cockpit. It has four tabs:

| Tab | What it does |
|---|---|
| **Overview** | Puts affected mailboxes and stale worker evidence first. |
| **Mailboxes** | Offers **Why is this mailbox late?**, Inbox check, reconnect, and pause or resume of content and history. |
| **Tuning** | Offers safe presets, grouped expert values, the requested and effective revision, and audited restore. |
| **Diagnostics** | Compares bounded buckets and keeps raw metrics collapsed. |

## Read mailbox progress

### Generations are not counters

Requested and completed generations are durability watermarks. They do not count messages or folders. They normally stay unequal until the complete requested generation converges. For this reason, the browser does not show a requested/completed fraction. Instead, it shows **Convergence**, **Historical folders**, and **Cached messages**.

### Pending content can rise and fall

Historical discovery and content acquisition are two bounded, independent lanes. Pending content rises when a discovery page commits requests. It falls when the content worker caches them. This also happens while recent-mail observations continue in their separate foreground session.

A sawtooth pattern in the pending count is healthy if both conditions are true:

- The folder and cached-message totals continue to advance.
- The queue eventually drains.

The cached-message denominator includes only current observations with `remote_present=TRUE`. Provider deletions and tombstones do not count as missing cached content.

### Readiness

API readiness covers local dependencies. The worker publishes readiness only after all of these steps are done:

- It holds exclusive leadership.
- Its schema, key, and storage checks pass.
- It recovered abandoned attempts.
- It reconstructed the accounts.

If `leaderPresent=true` and `ready=false`, treat it as a fault in worker startup, drain, or the health listener. Do not start an ad-hoc second worker.

## Startup and shutdown

The supported supervisor starts the processes in this order:

1. Start the API.
2. Wait for API liveness.
3. Start the worker.

This order prevents a race at first boot between master-key creation and the existing-key check of the worker.

During shutdown, the supervisor uses this order:

1. Stop the admission of API commands.
2. Terminate the worker and wait until it ends.
3. Stop the API.

The independently operated MariaDB service is not part of this shutdown sequence. If the worker cannot drain within its deadline, it must exit without releasing live authority. Then the process supervisor can prove that the worker is dead before it starts a replacement.

### Checks after startup

After startup, require all of these conditions:

- API readiness
- worker readiness
- exactly one leadership holder
- a clean expected schema
- stable pressure

The worker inspects durable convergence at bounded intervals of one second. The slower topology refresh only discovers changes in account configuration. A rising `pendingWakeSignals` value without account progress indicates a notifier or admission failure. Durable demand stays across a restart.

## Account controls

Use these API routes to control one mailbox account. Replace `{accountID}` with the account ID.

- **Suspend or resume:** `PATCH /api/mailboxes/{accountID}/status`. Suspension prevents new provider work. Suspension is mandatory before a derived reset.
- **Request observation:** `POST /api/sync/accounts/{accountID}/observations`. HTTP 202 means that the server durably accepted the generation. It does not mean that the work is complete.
- **Request a fresh connection:** `POST /api/sync/accounts/{accountID}/reconnect`. This route queues convergence and clears the durable delay. The account actor closes and replaces the session.
- **Inspect status:** `GET /api/sync/accounts/{accountID}`.
- **Inspect owner-scoped health:** `GET /api/sync/diagnostics`.
- **Pause or resume content or history only:** `PUT /api/admin/sync/accounts/{accountID}/lanes/{content|history}`. This control is idempotent. It affects new commands at safe boundaries. It never pauses freshness or operations.
- **Read or update the policy:** `/api/admin/sync/runtime-policy`. Inspect changes through `/audit`, and create a restored revision through `/revert`. A successful save is only requested state until `effectiveRevision` catches up.

### Uncertain operations

> **Warning:** Do not delete durable operations to clear uncertainty. An `uncertain` operation may have had a remote effect. Observe that effect before any retry.

If an uncertain item persists, escalate it. Send only these details:

- the opaque account identifier
- the opaque operation identifier
- the normalised phase and class

## Generation-fenced derived reset

The derived reset is an administrator-only recovery action. It is different from deleting the mailbox configuration or durable user intent.

1. Export the configuration and the backup receipts, and verify them.
2. Suspend the mailbox. Wait until the diagnostics report `disabled` and no active worker ownership.
3. Read the current reset generation from the account status or the diagnostics.
4. Submit `POST /api/admin/sync/accounts/{accountID}/derived-reset`. Send the owner, the expected reset generation, and the exact account ID as confirmation.
5. Record the returned prior and new generations and the count of deleted rows.
6. Make sure that the account state is clean and disabled. Then resume the account explicitly and request observation.

The reset runs in one transaction. The transaction does these things:

- It locks the suspended account.
- It rejects stale generations.
- It removes only account-scoped synchronisation evidence: observations, checkpoints, topology, content, and operations.
- It increments the reset generation.
- It keeps the mailbox configuration and the ownership of user intent separate.

A reset of a stale or active account fails closed.

## Failure guide

| Signal | Meaning | Action |
|---|---|---|
| API ready, worker unavailable | Local reads remain safe. Provider convergence is paused. | Inspect the supervisor and worker logs and dependencies. Then restart through the supervisor. |
| Worker live, not ready | Leadership or a startup or recovery check has not passed. | Check the schema, MariaDB, the master key, and writable raw storage. |
| Delayed accounts rising | Backoff because of provider, network, or authentication problems. | Inspect the normalised account condition. Do not bypass the bounded retry. |
| Pending content rising | Pressure on provider reads or raw storage. | Check capacity and object-store writability before you resume acquisition. |
| Freshness generation due for more than 60s | Protected Inbox work has not completed. | Open **Why is this mailbox late?** and check the worker, provider, and session evidence. Then request Inbox observation or reconnect. |
| History stalled, Inbox current | Intentional bulk throttling or a pause. | Inspect the history lane and the policy. Resume only when provider capacity is available. |
| Uncertain operations | The command outcome was ambiguous. | Allow observation and verification. Never reissue blindly. |
| Leadership present after worker death | The database session has not observed the connection death. | Restore database connectivity and let the supervisor wait. Never force a successor. |

## Auto-bin recovery

### Schema and migrations

Schema 139 includes these records:

- durable expiry for each message
- evidence of genuine arrival
- bounded due work
- portable binding records

The supervisor applies pending migrations before normal service startup. Ordinary image building does not migrate an existing installation.

Before you recreate an existing installation, do these steps:

1. Inspect the current clean schema.
2. Get approval for the migration and backup of retained data.

### Delayed expiry

Deadlines survive a worker restart and a closed browser. Due scans use durable, bounded rotation and retry times. When a deadline passes, an immediate provider move is not guaranteed.

Before you treat delayed expiry as lost work, check these items:

- account suspension
- safety mode
- the mapped Bin folder
- the normal operation status

If Bin is missing or unsafe, the deadline stays and execution is blocked. Awesonix Mail does not fall back to permanent deletion. Local-only mail cannot be scheduled.

### Do not force processing

- Do not delete schedules, arrival evidence, receipts, portable unresolved records, or uncertain operations to speed up processing.
- Never manually reissue the same placement blindly. Awesonix Mail verifies uncertain Bin attempts before a retry.

If you remove expiry, Awesonix Mail retires obsolete work that it has not sent. Removing expiry cannot reverse a move that Awesonix Mail already transmitted. If needed, restore the message through the normal message action. This action clears the expiry. The automatic 30-day cleanup of Bin and Spam is independent of auto-bin.

### Portable import

A portable import stages frozen deadlines without executing them. The import suspends and fences the represented mailbox sessions. It also disables the represented rules.

Resolve records only against a fresh, exact provider identity. Do not resolve them by subject, headers, hashes, or inferred current configuration. Unresolved records remain exportable. Rejected records stay permanently inactive.

### Roll back an upgrade

> **Warning:** Never mix an old runtime with a new schema. Never downgrade in place.

1. Stop the new artifact.
2. Restore the matching database, raw objects, search state, configuration, and key set. Follow [Restore your data](README.md#restore-your-data) in the operations guide.

Auto-bin down migrations refuse protected durable state. Meeting down migrations also refuse to run while meetings, meeting mail work, or received invitations exist. Do not bypass those guards. Restoring local data cannot undo provider actions that are already complete.
