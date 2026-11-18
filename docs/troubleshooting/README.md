# Troubleshooting

| **Last updated** | 2026-11-07 |
| **Scope** | Find and safely fix common problems with Awesonix Mail |

This guide helps users and home administrators find and fix common problems. Find your problem in the list below. Each problem has three parts: the **Symptom**, the **Cause**, and the **Fix**.

If the problem happens during installation, use the [installation guide](../installation/README.md) first. Each installation step has an "If it does not work" section.

> **Warning:** Do not delete containers, volumes, database files, raw mail objects, secrets, or recovery copies while you look for the cause. You can lose data that you cannot get back. Use read-only checks first.

## Contents

**Installation and start**

- [Docker is not available](#docker-is-not-available)
- [Port 8180 refuses connections](#port-8180-refuses-connections)
- [Mail seems missing after a rebuild of the container](#mail-seems-missing-after-a-rebuild-of-the-container)
- [The database is not available](#the-database-is-not-available)
- [The database set-up waits, fails, or is blocked](#the-database-set-up-waits-fails-or-is-blocked)
- [The container health stays starting](#the-container-health-stays-starting)

**Sign-in and settings**

- [The setup page appears instead of the sign-in page](#the-setup-page-appears-instead-of-the-sign-in-page)
- [A settings import is rejected](#a-settings-import-is-rejected)
- [The interface language does not change](#the-interface-language-does-not-change)
- [A new signature colour is rejected](#a-new-signature-colour-is-rejected)

**Mail synchronisation**

- [Synchronisation progress seems stuck](#synchronisation-progress-seems-stuck)
- [Search does not find text in some messages](#search-does-not-find-text-in-some-messages)
- [Remote retention is blocked or does not progress](#remote-retention-is-blocked-or-does-not-progress)
- [The Move or Copy folder list is hard to read](#the-move-or-copy-folder-list-is-hard-to-read)

**Phone app**

- [A change from the phone is pending or has a conflict](#a-change-from-the-phone-is-pending-or-has-a-conflict)
- [A notification is missing, late, or disappears](#a-notification-is-missing-late-or-disappears)

**Calendar and tasks**

- [A task reminder comes at an unexpected time](#a-task-reminder-comes-at-an-unexpected-time)
- [A notification opens an item that is not available](#a-notification-opens-an-item-that-is-not-available)
- [A received invitation is not in the calendar](#a-received-invitation-is-not-in-the-calendar)
- [A guest shows Invitation could not be sent](#a-guest-shows-invitation-could-not-be-sent)
- [An answer to an invitation was not sent](#an-answer-to-an-invitation-was-not-sent)

**AI assistant**

- [The AI assistant is off or the connection test fails](#the-ai-assistant-is-off-or-the-connection-test-fails)
- [AI analysis does not start, stays queued, or misses text](#ai-analysis-does-not-start-stays-queued-or-misses-text)

**Performance**

- [AI requests time out](#ai-requests-time-out)
- [Message actions are slow or a badge is wrong](#message-actions-are-slow-or-a-badge-is-wrong)

**Getting help**

- [What to collect](#what-to-collect)
- [What never to share](#what-never-to-share)
- [Synchronisation diagnostics](#synchronisation-diagnostics)
- [More guides](#more-guides)

## Installation and start

### Docker is not available

**Symptom**

You see one of these signs:

- An error message with `failed to connect to the docker API`.
- The Docker named pipe or the Docker socket is missing.
- Port 8180 refuses connections.

**Cause**

The Docker Engine does not run.

**Fix**

1. Start Docker Desktop or the Docker Engine.
2. Wait until `docker info` succeeds.
3. Go to the folder that holds your `docker-compose.yml`.
4. Check the standard instance:

   ```powershell
   docker compose ps   # run in the folder that holds your docker-compose.yml
   docker ps -a --filter name=awesonix-mail
   ```

5. Continue with [Port 8180 refuses connections](#port-8180-refuses-connections). A running Docker does not prove that the application is healthy.

### Port 8180 refuses connections

**Symptom**

The browser cannot connect to Awesonix Mail on port 8180.

**Cause**

The container is missing or does not run. Or the application is not live or not ready.

**Fix**

1. Show the status, the restart count, and the image of the container:

   ```powershell
   docker inspect awesonix-mail --format "status={{.State.Status}} restarts={{.RestartCount}} image={{.Image}}"
   ```

2. Show the last 200 lines of the container log:

   ```powershell
   docker logs --tail 200 awesonix-mail
   ```

3. Check the two health endpoints:

   ```powershell
   Invoke-WebRequest -UseBasicParsing http://127.0.0.1:8180/health/live
   Invoke-WebRequest -UseBasicParsing http://127.0.0.1:8180/health/ready
   ```

4. Use this table to read the result:

   | Result | Meaning |
   |---|---|
   | The container is missing. | The standard instance was not created, or someone removed it. |
   | `live` fails. | The HTTP process is not available. |
   | `ready` fails. | The process is alive, but a required dependency or startup condition is not ready. During the first database build, it fails on purpose. See [The container health stays starting](#the-container-health-stays-starting). |
   | The container restarts again and again. | Read the log before you restart it again. |

5. If another program uses port 8180, find that program. Do not silently change the port of the standard instance.

Use port 8181 only for an explicitly isolated temporary qualification test.

### Mail seems missing after a rebuild of the container

**Symptom**

You rebuilt the container, and now your mail seems to be gone.

**Cause**

The container might use the wrong volumes.

**Fix**

> **Warning:** Do not change anything before you know which volumes are attached. A wrong change can damage your data.

1. Show the mounts of the container and the Awesonix volumes:

   ```powershell
   docker inspect awesonix-mail --format "{{range .Mounts}}{{.Name}} -> {{.Destination}}{{println}}{{end}}"
   docker volume ls --filter name=awesonix
   ```

2. Make sure that the application container mounts these volumes:
   - `awesonix-mail` at `/srv/mail`
   - `awesonix-secrets` at `/srv/secrets`
   - `awesonix-logs` at `/srv/logs`
3. Make sure that the application does not mount `awesonix-db`. The separate local development container `awesonix-mail-db` owns that volume.
4. If you see volumes with other names or anonymous volumes, stop. Identify them before you change anything.

### The database is not available

**Symptom**

The page shows that Awesonix Mail is waiting for the database.

**Cause**

The database server cannot be reached or it refuses the user. Awesonix Mail waits and retries with a growing delay. It does not stop, and it does not run without the database.

**Fix**

> **Warning:** Do not delete rows or database files manually.

1. Read the logs of both containers.
2. Check network and DNS reachability.
3. Check the credentials, the TLS policy, and the grants.
4. Check the database capacity.
5. Use the recovery tools of the database service.

If you set the database in the container settings, correct the variables there and restart the container. The variables always win. To change a rotated password, change the variables.

For problems during installation, see [Step 2: Create the database](../installation/2-database.md#if-it-does-not-work).

### The database set-up waits, fails, or is blocked

**Symptom**

The page shows that the database set-up is waiting, failed, or blocked. Or the container log has a `database: failed class=...` line.

**Cause**

The log names the reason. These are the common ones:

- The database server is not reachable, or the password is wrong.
- A database update failed in one step.
- The saved settings file `database.json` is not valid.
- The deployment sets only some of the database variables.
- This installation was restored without `database.json` and without the variables.
- The schema is ahead of the image or dirty (see below).

**Fix**

There are three recovery paths.

1. **Environment variables always win.** To change a rotated password or another value, set the variables in the template or the compose file. Then restart the container.
2. **One-time unlock code.** This works when the build waits or fails, or when the saved file is invalid. It works only if the wizard saved the settings. It does not work for the variables.
   1. Read the container log. It shows a line like `bootstrap: unlock code=<code> valid=15m reason=<reason>`.
   2. Enter the code in the page. It has 8 characters, is valid for 15 minutes, and works once. After 5 wrong tries, it is void.
   3. If the code expired, submit any code. A fresh code appears in the log.
   4. After 5 wrong tries, the code is void. Wait one minute, then submit any code again. A new code appears in the log.
   5. The unlock renames `database.json` to `database.json.old` and reopens the database step. Enter the settings again.
   6. Delete `database.json.old` afterwards. It holds the old password.
3. **Restore without `database.json`.** The set-up stays locked. Put `database.json` back into the secrets folder, or set the variables. Then restart the container.

If the deployment sets only some of the database variables, set all of them or none. Then restart the container.

#### A dirty schema

The container was killed during a migration step. The status shows `schema_dirty` with a version, and the page says that a database update stopped half way. This is the one place where manual SQL is needed. A stop grace period of 120 seconds, as in the templates, avoids it. Keep it.

A second common cause is a restart or stop of the database server while the build runs. The step that runs is interrupted, and the build does not resume after the database returns. If the database is only not reachable before a step starts or between steps, the build waits and continues by itself.

> **Warning:** Do not run `UPDATE` on a database that you cannot restore. Make a copy first.

1. Stop the container.
2. The safest way is to restore the database backup that you made before the upgrade. See [Restore your data](../operations/README.md#restore-your-data).
3. The advanced way needs care.
   1. Find the migration file for the dirty version. In the image, the files are in `/srv/migrations` as `<version>_<name>.up.sql`. The image has no shell, so copy the file out with `docker cp <container>:/srv/migrations/<version>_<name>.up.sql ./`. This works on the stopped container. Or read the same file in the product source, in `product/database/migrations`.
   2. Read its statements. A database does not undo changes to its structure when a step stops. Some statements may have run, or all of them. Compare the file with the database. Use `SHOW TABLES`, `SHOW COLUMNS FROM <table>`, `SHOW INDEX FROM <table>`, `SHOW TRIGGERS`, and `SHOW FULL TABLES WHERE Table_type = 'VIEW'` for the objects that the file creates.
   3. Undo the partial effects. Drop only the tables, columns, indexes, triggers, or views that the file created. The schema must equal its state before that migration.
   4. Set the version back and clear the flag. Replace `<dirty version minus one>` with the number. For dirty version 192, use 191.

      ```sql
      UPDATE schema_migrations SET version = <dirty version minus one>, dirty = 0;
      ```

   5. Never mark a half-applied migration as done. Do not clear the flag and keep the dirty version.
4. Start the container. Startup runs the migration file again from its start.
5. If you are not sure, restore the backup instead.

The operations guide shows a worked case for migration 000192 in [Upgrade to version 0.110.0](../operations/README.md#upgrade-to-version-01100).

#### A schema ahead of the image

An older image runs on a database that a newer image changed. Awesonix Mail refuses to start. Use the matching image or a newer one. There is no automatic downgrade.

### The container health stays starting

**Symptom**

The container health shows `starting` for a long time.

**Cause**

The container answers on the public port from the first second. The health state is `starting` until the database build ends. The Docker start period is 60 minutes. A long first build on slow storage, for example spinning disks, can take many minutes.

If nobody finishes the first database step, the state turns unhealthy after the start period.

**Fix**

1. Open the web address of Awesonix Mail. The page shows the progress, for example `step n of m`.
2. Read the container log. It shows `database: step n of m ...` lines and a heartbeat every 30 seconds.
3. If the page asks for the database settings, enter them. See [Step 4: Run the setup wizard](../installation/4-setup-wizard.md).

## Sign-in and settings

### The setup page appears instead of the sign-in page

**Symptom**

The `/setup` page appears, but you completed setup before.

**Cause**

Usually, the application is connected to a new or empty external database.

**Fix**

> **Warning:** Do not finish setup again until you know the intended database and its backup status.

1. Check the database environment values. If you used the wizard, check that `database.json` is in the secrets folder.
2. Read the logs of the database service.

### A settings import is rejected

**Symptom**

Awesonix Mail rejects a settings file that you import.

**Cause**

One of these conditions applies:

- The file is not an Awesonix Mail settings JSON bundle.
- Someone edited the file after the export.
- The password or the backup passphrase is wrong.
- The version is not supported, the settings are malformed, or identifiers collide.

Awesonix Mail rejects a wrong passphrase or a changed bundle before it saves anything. The other errors also fail closed. A failed import is transactional: it does not leave a partial merge.

**Fix**

1. Make sure that the file is an Awesonix Mail settings JSON bundle.
2. Make sure that nobody edited the file after the export.
3. Enter the current password of the signed-in destination account. Do not enter a provider password.
4. Enter the same backup passphrase that you used for the export.

   > **Warning:** Never send the backup passphrase to support staff. Never put it in command arguments, logs, screenshots, or issue reports.

5. If the import fails again, keep the original file.
6. Collect only the displayed error text, the application version or image identity, and redacted logs.

After a successful import, mailbox connections stay suspended. This is intentional and is not an import failure. Review the settings of each connection before you resume it.

### The interface language does not change

**Symptom**

The interface shows a different language than you expect.

**Cause**

One of these conditions applies:

- The setting is **Automatic**. **Automatic** follows the preferred-language list of the current browser. It is not a fixed account language.
- You chose a language before sign-in. That choice stays in that browser only. Awesonix Mail does not copy it into the account automatically.
- A language catalog cannot be activated. Awesonix Mail then keeps the previous working language, or uses complete English during startup.

A language that you choose while signed in overrides the browser choice from before sign-in. The account choice follows you to other sessions and devices.

Stock provider folders, such as Inbox, Sent, Bin, and Spam, show a translated label. Custom provider folder names, message content, signatures, and contact data do not change language. Other values that users or providers own also stay unchanged. This is intentional.

**Fix**

> **Warning:** Do not edit database rows, browser storage, or catalog files to fix the language.

1. Open **Settings > General > Language & region**.
2. Check the selected value.
3. If a catalog cannot be activated, reload the application. Then select the language again.
4. Make sure that the application image and the browser assets come from the same build.
5. If the problem stays, keep these details for your report:
   - The selected language.
   - The application version and build.
   - The language list of the browser.
   - The displayed safe error or support identifier.

### A new signature colour is rejected

**Symptom**

After an upgrade, a newly displayed signature colour does not save.

**Cause**

The frontend, the preference validation in the backend, and the settings backup and restore must support the same palette. The running container might use an older image.

**Fix**

> **Warning:** Do not edit preference rows directly.

1. Make sure that the running container uses the digest of the newly installed image. It must not use an older image.
2. If the problem stays, keep the response status and the redacted error text for your report.

## Mail synchronisation

### Synchronisation progress seems stuck

**Symptom**

A mailbox shows **In progress** for a long time. Or **Pending content** goes up and down. Or the requested and completed generation numbers differ.

**Cause**

Usually, this is normal behaviour:

- The convergence generation is a durable watermark. It is not a counter that goes up step by step.
- A mailbox can stay **In progress** during a long historical scan. It changes to **Complete** only when the entire requested generation converges.
- During the initial synchronisation, **Pending content** can follow a sawtooth pattern. This pattern alone does not mean that the worker is stalled.
- The cached-message denominator counts only messages that are still present upstream. Messages that were deleted remotely or tombstoned do not keep a complete mailbox below 100 percent.

The sawtooth pattern works like this. Historical discovery adds a bounded page of content requests. Acquisition drains that page. Then the next discovery page adds more requests.

**Fix**

1. Use **Historical folders** and **Cached messages** to see the progress. Do not compare the requested and completed generation numbers.
2. Investigate further only if one of these conditions applies:
   - The progress of folders or cached messages stops across repeated observations.
   - Pending work does not drain.
   - The account enters the authentication, backoff, or faulted state.
   - Storage becomes unwritable.
   - Watchdog warnings persist.
3. For more checks, see the [synchronisation worker runbook](../operations/synchronization-worker.md).

### Search does not find text in some messages

**Symptom**

Search reports partial body coverage.

**Cause**

This is expected if a mailbox uses headers-on-demand. It is also expected if cache cleanup has evicted content. Header matches stay available. Search does not search message bodies that are not cached locally.

**Fix**

> **Warning:** Do not clear the search directory while the application runs.

1. As an administrator, open **Settings > Diagnostics > Search index**.
2. Check the counts for the mailbox.
3. If necessary, start a scoped rebuild.

A rebuild repairs the local projection. It does not fetch every remote body.

### Remote retention is blocked or does not progress

**Symptom**

Remote retention does not run, or it does not progress.

**Cause**

One of these conditions applies:

- The retention period is **Never**. New installations start with **Never**, so no retention runs.
- The mailbox is mirror-only. Mirror-only mailboxes cannot change the state at the provider.
- The mapped Bin folder is missing. This blocks execution.
- The messages are effectively unread or flagged. These messages are deliberately exempt. Awesonix Mail checks the state again before each submitted move.
- Provider authentication, throttling, or retry backoff is active. Restart-safe work can stay queued during that time.
- Someone cancelled the run. Cancellation stops new submissions. It does not undo a Bin intent that was already accepted.

**Fix**

> **Warning:** Never enable retention against a valued provider account to diagnose it. Never expunge Bin manually.

1. Open **Settings > Administration > Retention**.
2. Check the period. Awesonix Mail enforces any finite period that you save there automatically.
3. To stop enforcement, choose **Never**.
4. If a mailbox is mirror-only or has no mapped Bin, review the mailbox configuration. Do not change database rows.
5. Keep the redacted run state and the provider-operation logs for review.

### The Move or Copy folder list is hard to read

**Symptom**

After an upgrade, Move or Copy still shows one of these:

- A native selector.
- A centred desktop dialog.
- Duplicate raw folder names.
- Repeated `INBOX.` prefixes.

**Cause**

The browser might still use files from before the upgrade. Current builds remove the provider namespace prefix, such as `INBOX.`, from ordinary Move and Copy targets. Toolbar commands open searchable anchored menus. Right-click commands open searchable submenus.

**Fix**

1. Hard-refresh the browser.
2. If the problem stays, record the immutable container image ID and the name of the served JavaScript asset.
3. Report the problem with these details.

## Phone app

For pairing problems, see [Step 6: Connect your phone](../installation/6-phone.md#if-it-does-not-work).

### A change from the phone is pending or has a conflict

**Symptom**

A change that you made on the phone stays pending, or it shows a conflict.

**Cause**

- Full-access mobile writes are durable. They can stay queued while the device or the server is offline.
- A revision conflict deliberately keeps the mobile draft for review.

If the phone shows no write controls, the device might be paired with read-only access. Read-only paired devices correctly hide write controls.

**Fix**

> **Warning:** Do not clear the app cache or revoke the device before you keep any draft that you still need.

1. Reconnect the phone to the server.
2. Let synchronisation replay the same command identity.
3. If there is a conflict, open the Administration view in the mobile app.
4. Choose the retained command.
5. Compare the retained command with the current state on the server.

### A notification is missing, late, or disappears

**Symptom**

A notification does not arrive, arrives late, or disappears.

**Cause**

One of these conditions applies:

- Quiet hours are active. Quiet hours intentionally delay external delivery.
- Global external delivery is off, or the rule for the channel or category is off.
- Browser delivery has no active subscription or no browser permission.
- Mobile delivery has no active paired device or no permission for local notifications.
- You moved the message to Bin or deleted it permanently. This intentionally dismisses the matching notification and stops pending delivery.

If you restore the message, the old notification does not come back.

**Fix**

1. Check the in-app bell first. In-app items do not need browser or mobile permission.
2. Open **Settings > General > Notifications**.
3. Make sure that global external delivery is on.
4. Check the rule for the relevant channel and category.
5. For browser delivery, check the active subscription and the browser permission.
6. For mobile delivery, check the active paired device and the permission for local notifications.

   > **Warning:** Never collect endpoint URLs, device tokens, message subjects, senders, or notification payloads.

7. As an administrator, run the **Notification delivery test** card on **Settings > Diagnostics > Overview**.
8. Keep only the aggregate states of the queue and the provider, and redacted logs.

## Calendar and tasks

### A task reminder comes at an unexpected time

**Symptom**

A reminder for a task or event comes at an unexpected time, or it does not come.

**Cause**

- Date-only tasks use the configured reminder time in the display time zone of the Calendar.
- The optional overdue reminder runs once, on the next calendar day.
- Quiet hours intentionally delay external delivery.
- Completing, archiving, deleting, or rescheduling a task or event cancels pending reminders that no longer apply.

**Fix**

1. Check the configured reminder time and the display time zone of the Calendar.
2. Check the notification settings. See [A notification is missing, late, or disappears](#a-notification-is-missing-late-or-disappears).

### A notification opens an item that is not available

**Symptom**

You open a notification, and the item is not available.

**Cause**

Someone deleted the source item. Or the item moved outside the access of the device. Or the item is outside the offline Calendar window.

**Fix**

> **Warning:** Do not change notification rows directly.

1. Refresh the view.
2. Try again.

### A received invitation is not in the calendar

**Symptom**

You got a meeting invitation or update by mail, but the calendar does not show it or does not show the change.

**Cause**

- The mail is in Spam. Awesonix Mail does not add invitations from Spam.
- The mail arrived before Awesonix Mail started to add invitations for the mailbox. Its card shows **Read-only**.
- Awesonix Mail is still processing the mail. Its card shows **Pending**. A signature check can keep an update waiting for up to 7 days.
- The update could not be confirmed as coming from the organiser. Its card shows **Not applied**.
- You removed the meeting earlier. A cancellation does not bring it back.

**Fix**

1. Open the mail and read the label on its card.
2. If the mail is in Spam and you trust it, move it to the Inbox. The meeting is then added.
3. For **Not applied**, ask the organiser to send the update again from their usual mail account.
4. For **Removed**, choose **Yes**, **Maybe**, or **No** on the card to add the meeting back.

### A guest shows Invitation could not be sent

**Symptom**

In your meeting, a guest shows **Invitation could not be sent**.

**Cause**

The mail server refused the mail for that guest, or it did not accept the mail after about 4.6 days of retries. A wrong address is the most common reason.

**Fix**

1. Check the address of the guest.
2. If it is wrong, remove the guest and add the correct address. Save the meeting and choose **Send**.
3. If the address is correct, check that the sending mailbox is interactive and can send other mail.

### An answer to an invitation was not sent

**Symptom**

After you answer **Yes**, **Maybe**, or **No**, Calendar says that your answer is saved but not sent.

**Cause**

Your answer goes from the mailbox that received the invitation. That mailbox only mirrors mail, or it was removed. Awesonix Mail does not use another mailbox without asking you.

**Fix**

1. Ask your administrator or the owner of the mailbox to make it **Interactive**.
2. Answer again.

## AI assistant

### The AI assistant is off or the connection test fails

**Symptom**

AI features are not available. Or the **Test** action of a connection fails or reports an unreadable response.

**Cause**

One of these conditions applies:

- AI features are not enabled for your account.
- No enabled connection matches the active local/cloud posture. If **Local providers only** is selected, cloud-posture connections are intentionally skipped.
- The container cannot reach the endpoint. A service that the host can reach is not always reachable inside Docker.
- The model name is not available.
- The API key is not valid.
- The selected model cannot follow the JSON response contract.

Local reasoning models may wrap valid JSON in a reasoning block or a Markdown fence. Awesonix Mail accepts that wrapper, and still validates the enclosed result strictly. Local OpenAI-compatible runtimes also receive a purpose-specific response schema if they support constrained JSON-object generation.

**Fix**

1. Open **Settings > General > AI configuration**.
2. Turn on **Enable AI features for my account**.
3. Make sure that at least one enabled connection matches the active local/cloud posture.

   > **Warning:** Do not relabel a cloud endpoint as local to bypass the **Local providers only** boundary.

4. Use the **Test** action of the connection. This action checks provider reachability and the structured JSON response that analysis needs.
5. If **Test** is not available, enable the connection. The active local/cloud posture must also admit the connection.
6. If the endpoint is not reachable, use an address that the container can reach. On Docker Desktop, local Ollama commonly uses `http://host.docker.internal:11434`.
7. If the test reports an unreadable response, choose a model that supports JSON output. Or adjust the model configuration of that provider.

### AI analysis does not start, stays queued, or misses text

**Symptom**

One of these happens:

- Manual analysis does not start.
- Re-analysis stays queued.
- Analysis reports omitted or truncated messages in long threads.
- Automatic analysis does not run.

**Cause**

- Manual analysis needs a selected message with content that is available locally.
- Re-analysis may stay queued briefly during a bounded provider retry. It must reach a terminal result.
- Awesonix Mail asks a local llama.cpp or Ollama server for its context window, and plans prompts for that window. For local providers, the cap is 8192 tokens.
- Automatic analysis is configured separately for each mailbox, and it starts off.

Current builds terminalise exhausted legacy jobs. The control does not keep rotating indefinitely.

Legacy HTML mail can contain large runs of layout whitespace. Current builds lazily reproject that retained raw message before AI use. The useful text then reaches the provider, and formatting noise does not displace it.

**Fix**

1. For manual analysis, select a message.
2. Wait for any on-demand body fetch to finish. Then try again.
3. If analysis reports omitted or truncated messages in long threads, start the local server with a larger context window. Use at most 8192 tokens. A larger window makes each answer slower on a CPU.
4. For automatic analysis, turn it on for the mailbox.
5. Check the exclusions for the mailbox, the folder, and the sender.
6. Check the separate toggle for attachment analysis.

Suggestions are advisory. Low confidence, conflicting dates, or missing evidence need your review. Awesonix Mail deliberately does not create or send anything without your confirmation.

## Performance

### AI requests time out

**Symptom**

The assistant reports that the provider did not respond in time. Or the server log shows `failure=timeout` or `failure=retry_exhausted` for AI work.

**Cause**

A local model that runs on a CPU can take minutes for one request. Or the local server cuts off the answer. Awesonix Mail rejects a cut-off answer as unreadable.

**Fix**

1. Open **Settings > General > AI configuration**.
2. Raise the **Timeout** of the connection. The default is 300 seconds for local connections and 60 seconds for cloud connections.
3. Make sure that the local server does not cap generated output below about 2048 tokens. For briefings, also check **Context window** and **Answer limit** of the connection.
4. To see the duration of each background provider call, check the `ai-work: provider call` log line. This line contains no mail content.

### A briefing fails or is partial

**Symptom**

A briefing shows a failure message. Or it is partial: it has lines that say "summarised from subject only", or Details lists mail as unavailable or as "could not be checked for replies". The server log has `briefing-engine:` lines.

**Cause**

- The AI provider was not available, or no connection was eligible. The briefing fails.
- More messages failed than the limit. The limit is 3 messages or 10 % of the messages in the period, whichever is larger. The log shows `coverage_incomplete`. The briefing fails.
- The context window is too small for one message (`window_too_small`), or the model gave no usable answer for any group (`model_unusable`). The briefing fails.
- A single message could not be summarised after retries. It shows as a subject-only line, and the briefing is partial.
- A message was deleted while the briefing ran. It shows as unavailable.
- The server did not know the reply state of a mail. The briefing counts it and never lists it as unanswered.

**Fix**

1. Open **Settings > General > AI configuration** and select **Test** for the connection.
2. If the model has a small window, set **Context window (tokens)** to the real size. Leave it empty to use the size that the server reports, or 131,072 tokens.
3. Set **Timeout** higher for a slow local model. Awesonix Mail also learns the speed of each connection.
4. Select regenerate on the briefing. A partial briefing is not an error. Regenerate it if you want the lines summarised.
5. To see each call, check the `briefing-engine: call` log lines. They contain no mail content.

### Message actions are slow or a badge is wrong

**Symptom**

Moving, flagging, deleting, or changing the read state of a message is slow. Or a message badge disagrees with the state at the provider.

**Cause**

- Multiple accepted intents may legitimately reduce to fewer provider operations.
- Multiple compatible operations may use one IMAP command.
- One slow IMAP stage does not mean that the queue is stuck.
- A badge can disagree with the stored provider observation while no intent is active. This is a projection defect, not unfinished provider work.

A quiet healthy endpoint has no active operation queue and no active intent queue. It also has no pending search changes.

**Fix**

> **Warning:** Do not restart the application only to make a slow operation faster.

1. Open the browser developer console.
2. Move, flag, delete, or change the read state of the message. The browser writes records that start with `[awesonix-mail-action]`.
3. Show or clear the latest events with these commands:

   ```javascript
   window.awesonixMailDebug.snapshot()
   window.awesonixMailDebug.clear()
   ```

4. Find the `traceId` of the action.
5. Show the backend provider-transition records:

   ```powershell
   docker logs --since 10m awesonix-mail 2>&1 | Select-String "mail-action:"
   ```

6. Match the references from durable acceptance with these backend records.
7. If a provider command is slow or fails transiently, confirm that a later correctness slice succeeds.
8. Confirm that the durable queue drains.
9. If a badge disagrees with the stored provider observation, record both readings in your report.

The browser events cover these phases: initiation, optimistic projection, next paint, durable acceptance, projection observation, convergence, and failure. The interface keeps the latest 500 events in memory. Durable acceptance also reports the server duration and opaque references to the intent and revision.

The worker logs each processed provider operation as `sync-operation: processed`. The record shows the kind, the decision, whether a command was sent, and the duration.

## Getting help

### What to collect

Collect these items for a report:

- The container status and the restart count.
- The immutable image ID.
- The health responses.
- Redacted logs.
- The source commit.
- The schema status.
- The names of the volume mounts.

For AI problems, these items are also safe:

- The connection kind.
- The redacted host of the endpoint.
- The model name.
- The local/cloud posture.
- The response status.
- The application image identity.
- Logs without content.

### What never to share

Never publish or send these items:

- Provider passwords and database credentials.
- The master key.
- Session cookies and setup tokens.
- Private message content.
- Raw backup contents.
- The backup passphrase.
- API keys.
- Message bodies and attachments.
- Prompts that contain private mail.
- Raw provider responses that may echo private mail.
- Endpoint URLs, device tokens, message subjects, senders, and notification payloads.

### Synchronisation diagnostics

The authenticated `GET /api/sync/diagnostics` response shows these items:

- The reachability, readiness, and leadership of the worker.
- The global durable pressure.
- The account generations of the owner, plus counts of operations and content.

Diagnostics contain opaque identifiers and normalised state. They do not contain message subjects, senders, recipients, bodies, provider text, or credentials.

Administrators use `GET /api/admin/sync/diagnostics` for the mailbox projection of all users. Administrators can also check the path-redacted capacity at `GET /api/admin/storage-capacity`.

### More guides

- [Installation guide](../installation/README.md)
- [Deployment guide](../deployment/README.md) for standard commands
- [Operations guide](../operations/README.md) for standard commands
- [Synchronisation worker runbook](../operations/synchronization-worker.md)
- [Help](../help/README.md)
