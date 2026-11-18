# Administrator Guide

| **Last updated** | 2026-11-07 |
| **Scope** | Users, account recovery, mailboxes, storage, retention, diagnostics, security, portable settings, and contacts capacity |

This guide is for the administrator of a home or small-office installation of Awesonix Mail. You do not need to be an IT expert. Each section starts with a task, such as adding a user or pausing a mailbox.

To install Awesonix Mail and create the first administrator, follow the [installation guide](../installation/README.md). For backups, upgrades, and monitoring, read the [operations guide](../operations/README.md).

## Contents

- [Where to find the administrator pages](#where-to-find-the-administrator-pages)
- [Manage users](#manage-users)
- [Help a user who is locked out](#help-a-user-who-is-locked-out)
- [Approve phone access](#approve-phone-access)
- [Manage mailboxes](#manage-mailboxes)
- [Manage storage and search](#manage-storage-and-search)
- [Set remote retention](#set-remote-retention)
- [Use diagnostics](#use-diagnostics)
- [Protect and recover data](#protect-and-recover-data)
- [Security](#security)
- [Move settings to another installation](#move-settings-to-another-installation)
- [Contacts capacity and recovery](#contacts-capacity-and-recovery)

## Where to find the administrator pages

| Page | Use it to |
|---|---|
| **Administration** | Add and manage users, and see all mailboxes of the installation. |
| **Settings > Administration** | Set the rules for synchronisation, storage, and retention. |
| **Settings > Diagnostics** | Check the health of synchronisation, search, and notifications. |

Only administrators see the **Administration** and **Diagnostics** pages. The server APIs enforce the same rule.

Dates and times on these pages use your own date and clock preferences under **Settings > General**. Times use the local time zone of your browser. A feature can show a different time zone only if it has its own time-zone setting.

## Manage users

Awesonix Mail has its own local user accounts. These accounts are independent of the email providers.

### The founding administrator

The setup wizard at `/setup` creates the first administrator. You can stop the wizard and continue it later. While setup is unfinished, you can review the administrator details.

When you finish setup, the wizard creates exactly one initial administrator. Then the setup pages close permanently. For the wizard steps, see [Run the setup wizard](../installation/4-setup-wizard.md).

This first account is the **founding administrator**. It has these protections:

- Nobody can demote, suspend, or delete the founding administrator.
- No other administrator can reset its password or authenticator.

These rules prevent another person from removing or taking over the administration of the installation. For a lost password or authenticator, see [Recover the founding administrator](#recover-the-founding-administrator).

You manage all other users on the **Administration** page.

### Find a user

The user directory on the **Administration** page has a search field. You can filter the directory by role or by status. The directory shows users in pages of limited size.

- Expand a user to see the mailboxes that the user owns.
- Look at the user list to see if a user has an authenticator enabled.

### Add a user

1. Open **Administration > Users > Add user**.
2. Enter the details of the new account.
3. If the user must manage the installation, give the user administrator rights.
4. Enter a temporary password.
5. Create the account.

The user must change the temporary password after the first sign-in.

The email address of the account matters for meetings. A meeting organiser on this server can let guests edit a meeting with **Modify event**. A user can edit only when the organiser invited this account email address.

### Change the role of a user

1. Open the action menu of the user.
2. Choose **Promote to administrator** or **Demote to user**.

You cannot change the role of the founding administrator.

### Suspend, activate, or delete a user

A user account has one of these statuses:

| Status | Effect |
|---|---|
| **Active** | The user has normal access. |
| **Suspended** | The user cannot sign in. Awesonix Mail keeps the data of the user. |
| **Deleted** | Awesonix Mail permanently removes the user and all associated data. |

To suspend a user, choose **Suspend user** in the user table. Suspending a user also revokes the paired phones of that user. To give access again, choose **Activate user**.

> **Warning:** Deleting a user permanently removes the user and all associated data. This data includes mailboxes, sessions, and history.

To delete a user:

1. Choose **Delete user** in the user table.
2. Confirm the deletion.

### Registration policy

In this release, an administrator must create every account. This is the only complete way to enrol users. The **Administration** page shows the active registration policy. The default policy is **Disabled**.

The stored policy model and the backend contracts reserve three more modes: invitation, approval, and public. The browser does not have complete workflows for these modes. It has no complete configuration, invitation acceptance, approval, email verification, or public sign-up. Do not advertise or enable these modes as working features.

## Help a user who is locked out

The user list shows if a user has an authenticator enabled. You can reset the password or the authenticator of a user.

These rules apply to both resets:

- You must confirm each reset explicitly.
- Awesonix Mail keeps an audit record of each reset, with sensitive details removed.
- You cannot see old passwords, authenticator seeds, authenticator codes, or recovery codes.
- You cannot reset your own password or authenticator with these controls.
- You cannot reset the founding administrator.

### Reset a password

> **Warning:** Awesonix Mail shows the temporary password only once. Copy it before you close the dialog.

1. Open the action menu of the user.
2. Choose **Reset password** and confirm.
3. Copy the temporary password and give it to the user in a safe way.

Awesonix Mail creates a strong, random temporary password. It also signs the user out of all browsers. The user must change the password at the next sign-in.

### Reset an authenticator

1. Open the action menu of the user.
2. Choose **Reset authenticator** and confirm.

This reset deletes the encrypted TOTP seed and the stored digests of the recovery codes. It also deletes pending sign-in challenges and signs the user out of all browsers. The user can then set up an authenticator again. The [user guide](../user-guide/README.md) explains how.

### Recover the founding administrator

No administrator can reset the founding administrator from the browser. If the founding administrator loses the password, or the authenticator and all recovery codes, run the recovery command on the server.

> **Warning:** The recovery command gives full access to the founding administrator account. Anyone with shell access to the container host can run it. Protect this access.

1. Open a terminal on the host that runs the Awesonix Mail container. On Synology, connect with SSH.
2. Find the name of the container with `docker ps`. This guide uses `awesonix-mail`.
3. Run the command:

   ```sh
   docker exec awesonix-mail awesonix-mail recover-founder --yes
   ```

   If the authenticator and all recovery codes are also lost, add `--reset-authenticator`.
4. Copy the temporary password from the output. The command shows it only once.
5. Sign in with the username of the founding administrator and the temporary password. Awesonix Mail then asks for a new password.
6. If you reset the authenticator, set up a new one. The [user guide](../user-guide/README.md) explains how.

The command signs out all sessions of the founding administrator. It also clears the failed sign-in attempts for the username and the email address. It writes an entry to the security audit, and the founding administrator gets a security notification. Without `--yes`, the command only explains what it would do and changes nothing.

Keep the password and the recovery codes in a password manager. Consider a second administrator account for daily administration.

## Approve phone access

The owner of an account approves a phone from **Settings > Account > Device access**. The owner must be signed in. Do not give an account password or a provider password to pair a phone. For the pairing steps, see [Connect your phone](../installation/6-phone.md).

The owner chooses one of these access profiles when creating the pairing code:

| Access profile | What the phone can do |
|---|---|
| **Full mobile access** | Use Mail, Contacts, Calendar, Tasks, account, and notification features. Each feature is controlled by its own capability. |
| **Read-only mobile access** | Use the same features, but without permission to make changes. |

Administrator diagnostics on the phone are a separate option. This option is off by default. It works only if the owner is an administrator and the phone has **Full mobile access**.

To pair a phone:

1. Open **Settings > Account > Device access**.
2. Choose the access profile and, if needed, the administration option.
3. Check the server address for the phone.
4. Select **Create pairing code**.
5. Scan the QR code or type the code in the app. The code works once and only for a short time.

If you revoke a phone, the phone cannot reach the server again. After the phone reconnects, the app deletes its protected data.

## Manage mailboxes

### View all mailboxes

**Administration > Mailboxes** lists every mailbox in the installation. You can search by mailbox or by owner. You can filter by state: attention, healthy, synchronising, or paused.

Each row shows this information:

- The owner of the mailbox
- The synchronisation health
- The latest activity
- The number of local messages
- The safety mode
- The incoming server endpoint
- The download mode

The **Administration** page gives you health and pause controls for the whole installation. It does not give routine access to the credentials or mail of other users.

### Pause or resume a mailbox

- Choose **Pause sync** to stop automatic monitoring and sending. The pause stays in effect after a restart. Awesonix Mail keeps the local history.
- Choose **Resume sync** to start monitoring and sending again.

### What the owner of a mailbox manages

The owner manages these items under **Settings > Account > Mailboxes**:

- Credentials
- Provider endpoints
- Folder mappings
- Connection tests
- Removal of the connection

Removing a connection needs confirmation. It does not change the mail at the provider. Awesonix Mail keeps the local history that it downloaded.

### Add a mailbox with a provider preset

When you add a mailbox, you can choose from 19 preset providers. A preset fills in the host, port, TLS mode, and credential type. Manual setup is always available.

Some providers require an app password. The provider list tells you how to get one. For the full steps, see [Connect your mailbox](../installation/5-mailbox.md).

### Choose the safety mode

| Safety mode | Effect |
|---|---|
| **Mirror-only** | The default for new mailboxes. The backend allows read-only access. No flags, moves, deletions, or sends are possible. |
| **Interactive** | Full read and write access to the provider. |

> **Warning:** Interactive mode can change the mail at the provider.

A mirror-only mailbox cannot send meeting invitations or answers. Awesonix Mail also does not move meeting mail out of its Sent folder or Inbox.

An interactive mailbox can return to mirror-only. The switch stops new changes at once; changes already started finish. Blocked changes wait and continue when the mailbox is interactive again. A message that was already delivered is never sent twice.

To make a mailbox interactive:

1. The owner of the mailbox reads the warning about changes at the provider.
2. The owner changes the mailbox to **Interactive**.

Awesonix Mail records this change in the audit log.

### Check the health of a mailbox

Active mailboxes synchronise automatically over IMAP. Each pass downloads a limited batch, with the newest messages first.

**Mailbox health** shows the current state. It also shows the number of messages that are stored locally. If a mailbox is suspended, no more automatic passes run. Messages that are already imported stay in local storage.

### Fix a stalled mailbox

Open **Sync actions** and choose one of these actions:

| Action | Use it to |
|---|---|
| **Request observation** | Queue a fresh check of the mailbox. |
| **Reconnect** | Replace a provider session that is stalled. |

These actions add synchronisation work for the owner of the mailbox. They do not bypass these safety rules:

- A suspended mailbox
- An authentication failure
- The wait time between retries
- The safety rules of the provider

### Set the attachment limit

The attachment limit is the total size of all attachments in one draft.

1. Set `AWESONIX_MAIL_ATTACHMENT_LIMIT_MIB` to a value from 1 through 100.
2. Start the Awesonix Mail core. The core reads the value only at startup.

The default is 20 MiB. Users see the active limit in the compose workspace.

### Mail that disappears at the provider

Awesonix Mail has no setting for administrators or users to change this behaviour.

- If reconciliation confirms that a message is gone at the provider, Awesonix Mail removes it at once. This includes the message, its conversation, and its search data.
- Content that no message uses any more is removed after a fixed 24-hour safety window. A final reference check runs first.
- To keep mail independent of the provider, users must create their own local folders.

## Manage storage and search

### Where Awesonix Mail stores mail

Awesonix Mail stores the raw RFC 5322/MIME objects under `AWESONIX_MAIL_CONTENT_PATH`. Each object is content-addressed. In the Docker image, the default path is `/srv/mail/objects`.

Synchronisation works like this:

- Each mailbox progresses independently.
- Checkpoints survive a restart.
- Awesonix Mail refreshes the remote flags in rotating windows of limited size.
- After a provider failure, Awesonix Mail waits longer before each retry.
- If the UIDVALIDITY of a folder changes, Awesonix Mail creates a new active generation of the folder. It does not show the old generations.

### Quarantine of damaged content

Awesonix Mail counts repeated failures for the same local message. When the count reaches the limit, it quarantines that content. Other synchronisation work continues.

These problems do not count towards the limit and do not reset it:

- Connectivity problems
- An unavailable provider
- Throttling by the provider
- Authentication or authorisation failures
- Cancellation
- Cursor recovery

Awesonix Mail quarantines raw content at once if its identity or size is wrong. It never deletes the last known-good local message before it validates a replacement.

To change the limit:

1. Open **Settings > Administration > Synchronisation > Tuning**.
2. Set **Content failures before quarantine** to a value from 2 through 20. The default is 5.

### Rebuild the search index

The search index is stored in `/srv/mail/search` by default. The index is disposable, because Awesonix Mail can rebuild it. A rebuild uses the metadata and body content in MariaDB. A rebuild runs at startup or when you start it in the browser.

If the index is corrupt, Awesonix Mail quarantines it and replaces it automatically. The raw mail and the database stay the source of truth.

**Settings > Diagnostics > Search index** shows the header and cached-body coverage of each mailbox. You can rebuild the index for one mailbox at a time:

- The rebuild continues safely after a restart.
- The current index stays active while the rebuild runs.
- A rebuild never clears another mailbox.

### Set cache quotas

**Settings > Administration > Storage** shows the physical storage capacity. It also lets you manage the cache of each user.

One default quota applies to each user. It limits the total cached remote content of all mailboxes of that user. A value of zero means unlimited.

You can give one user a different quota. This exception records these details:

- A reason, which is required
- The approving administrator
- The creation time
- An optional review date

To remove the exception, return the user to the global default.

The user table shows the cached bytes, the message count, and the effective quota of each user. It also shows the utilisation and a breakdown for each mailbox.

### Clean the cache of a user

Choose **Clean user cache** to create reviewed cleanup jobs for the mailboxes of that user. The jobs remove only eligible content.

- The preview and the progress show counts and the bytes that the cleanup can free. They do not show message content.
- Cleanup keeps the headers, the flags, the folder placement, and the remote mail.
- Cleanup deletes a content-addressed object only after its last reference is gone.

### Notes usage and limits

The user table on **Settings > Administration > Storage** has a **Notes** column. It shows the note storage of each user against the storage limit, and the number of notes of that user. The summary above the table shows the total note storage and the total number of notes of the installation.

Note usage counts the stored attachments and the document bytes of each note. Awesonix Mail charges the usage to the owner of the page. When a writer adds a note or an image to a shared page, the usage counts for the page owner, not for the writer. Merge bases and share links do not count.

The **Notes limits** card on the same page sets the limits:

- The storage per user. The default is 100 MiB. The range is 10 MiB to 100 GiB.
- The number of notes per user. The default is 10,000. The range is 100 to 1,000,000.
- The size of one attachment. The default is 20 MiB. The range is 1 MiB to 200 MiB.

A lower limit never deletes content. When a user reaches a limit, Awesonix Mail refuses the next write and shows the limit to the user. A user over a limit can still read and delete notes. Two administrators cannot overwrite each other: the card refuses a save when the limits changed since the page loaded. Reload and try again.

Private notes count with their encrypted size. Their bodies and images are encrypted with the installation key, so a database backup without `/srv/secrets` cannot show them. The administration pages show usage numbers only, never note content.

The settings export of a user carries at most 256 MiB of images in total. A user with more note and contact images than that cannot export settings until some images are removed.

Choose **Recount usage** on the card when the usage looks wrong, for example after a restore. Awesonix Mail recounts the usage of every user from the stored notes and attachments and reports how many users it corrected.

## Set remote retention

Remote retention moves old messages to Bin at the provider. **Settings > Administration > Retention** has one global period for current and future mailboxes.

The period options are 3, 5, 10, 11, 15, or 20 calendar years, or **Never**. New installations start with **Never**.

> **Warning:** Awesonix Mail enforces any period other than **Never** automatically. When you save such a period, the affected mailboxes become due at once.

### Set the global period

1. Open **Settings > Administration > Retention**.
2. Choose and save the global period.

**Preview run** is optional. It shows the counts of eligible, exempt, and blocked messages. You can start a run immediately from the preview.

A change to the global period does these things:

- It updates every mailbox that uses the global period.
- It keeps the mailbox exceptions.
- It cancels the active runs of the affected mailboxes between messages.

After that, the worker checks each enabled mailbox about once a day. It moves eligible messages to Bin. To stop enforcement, choose **Never**.

### Set an exception for one mailbox

A mailbox exception requires a reason. It records the approving administrator and the creation time. It can also have a review date. The exception stays when the global period changes later.

When you save a finite period for an exception, the mailbox becomes due at once. Choose **Never** to stop enforcement for that mailbox. To remove the exception, choose **Use global**.

### Which messages a run moves

- Awesonix Mail uses the received date and the current state of each message.
- Unread and flagged messages are exempt. A later run checks them again if their state changes.
- Mirror-only mailboxes are blocked.
- Mailboxes without a mapped Bin folder are blocked.

### How a run works

- A run moves messages in a fixed order, with the oldest first.
- A run limits its speed and stores each move durably.
- A run uses the same Bin operation as other moves. It never deletes messages permanently.
- A run continues after a restart.
- You can cancel a run between messages.
- A run keeps its progress and audit records, with sensitive details removed.

Each provider must be qualified separately before you use retention against real accounts. If retention does not progress, see the [troubleshooting guide](../troubleshooting/README.md).

## Use diagnostics

### The Overview page

**Settings > Diagnostics > Overview** shows the state of the whole installation. The summary shows these items:

- The database schema
- The worker leadership
- Ready and delayed mailboxes
- Durable operations
- Content acquisition
- Wake pressure

Below the summary is a mailbox directory with a search field. Mailboxes that need attention come first. Each mailbox shows its owner, its state, and its progress. The directory also shows pending and uncertain work, and the time of the latest state.

### Read mailbox progress

Mailbox progress has three separate readings:

| Reading | Meaning |
|---|---|
| **Convergence** | Shows if the current synchronisation generation is complete. The generation is a watermark, not a counter of messages. |
| **Historical folders** | Shows the completed membership scans against the current folders that Awesonix Mail found. |
| **Cached messages** | Shows the current messages that are available locally against the current provider observations. |

Messages that are gone at the provider are not part of the count of discovered messages. Messages that are marked as deleted are also not part of it.

**Pending content** can go up and down during a healthy first synchronisation. Each new page of historical messages creates another batch to download.

### Recover a stalled mailbox

Each mailbox has administrator-only **Recovery actions**:

- **Request observation** asks for a durable new check of the mailbox.
- **Reconnect** replaces the provider session.

Both actions record the owner of the mailbox. Both actions pass the same ownership and suspension checks as the recovery actions of the owner. Your administrator rights do not bypass the safety rules of synchronisation.

To rebuild the search index, use the **Search index** page. Offline restore and offline index recovery are operator commands. They are not routine browser actions.

### How uncertain operations recover

Sometimes Awesonix Mail does not know if a message operation completed. It then checks the source and the destination before it does anything else. It does not repeat the change at the provider. This recovery runs without your help.

- These checks get a longer time limit at the provider than a new interactive command.
- After a temporary failure, the check is retried durably.
- If convergence is already pending, **Request observation** joins the pending work. It does not add to the backlog.
- If the provider session itself is stalled, use **Reconnect**.

### The Search index page

**Settings > Diagnostics > Search index** shows all mailboxes of the installation. The summary shows the header coverage and the cached-body coverage separately.

The mailbox directory has a search field and shows mailboxes in pages. For each mailbox, it shows the owner, any incomplete index, any active rebuild, and the latest update.

- By default, the page shows incomplete or rebuilding mailboxes first.
- You can switch the view to all, rebuilding, or complete mailboxes.
- Rebuild and cancel work on one mailbox at a time.
- Recent activity shows the owner of each mailbox.

### Test notification delivery

The **Notification delivery test** card is on **Settings > Diagnostics > Overview**. It sends a test item to you only, through the real notification queue. It then shows the counts for each channel and state.

Use this test before you change the configuration. It shows if a provider is disabled or missing, or if the queue is under pressure. The test never shows the items, endpoints, tokens, or provider responses of other users.

### Diagnostic APIs

The browser uses the administrator-only `GET /api/admin/sync/diagnostics` contract. The `GET /api/sync/diagnostics` contract is limited to the data of the owner. It is for personal use and for diagnostic tools.

Neither contract exposes any of these items:

- Provider credentials
- Message subjects, senders, recipients, bodies, or attachments
- Raw provider errors
- Storage paths or content keys

## Protect and recover data

Database backup and restore belong to the MariaDB service, which you manage separately. Coordinate each database recovery generation with the matching `/srv/secrets` and `/srv/mail` data. Check it with the [operations guide](../operations/README.md). Note images live next to the contact photos on the content mount, and private notes open only with the installation key. Restore the database, the content mount, and the key together.

> **Warning:** Do not delete an orphaned content object only because the live database does not refer to it. It can belong to retained deletion history or to a recovery lineage.

The content-integrity diagnostics separate damaged objects that are in use from orphaned objects. Before you propose to delete anything, do these steps:

1. Find out where the orphaned object came from.
2. Make a verified recovery copy.

Cadence findings do not include work that provider failures block. If warnings repeat, do not delete queue rows or restart again and again. Instead, check these items:

- The reported work kind
- The due state
- The authentication and retry state
- The redacted logs

## Security

| Area | Protection |
|---|---|
| Session cookies | The cookies are HttpOnly and SameSite=Lax, with Path=/api. The browser storage holds no session data. |
| CSRF protection | Requests that change state need a token header that matches a readable cookie. |
| Sign-in throttling | Awesonix Mail allows 5 failed attempts per identifier in each 15-minute window. It also throttles by IP address. |
| Client address | Sign-in throttling, audit records, the second-factor check, and the limits of public note links use one client address rule. Behind a trusted reverse proxy, Awesonix Mail uses the rightmost `X-Forwarded-For` entry that is not a trusted proxy. |
| Provider credentials | Awesonix Mail encrypts them at rest with AES-256-GCM. The master key is at `AWESONIX_MAIL_MASTER_KEY_PATH`. |
| Message display | Awesonix Mail sanitises the HTML and shows it in an isolated, sandboxed frame. Remote images are blocked by default. |

Each user manages their own lists of trusted senders and trusted domains under **Settings > Account > Privacy**.

### Set the trusted proxies

`AWESONIX_MAIL_TRUSTED_PROXIES` names the reverse proxies whose `X-Forwarded-For` entries count. The value is a comma-separated list of IP addresses or CIDR networks.

1. Keep the value empty for the default: loopback, `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`, `fc00::/7`, `169.254.0.0/16`, and `fe80::/10`.
2. To trust only your proxy, set its address or network, for example `172.18.0.0/16`.
3. To trust no proxy, set `none`.
4. Start the Awesonix Mail core. The core reads the value only at startup. An invalid value stops the startup with an error.

With the default, a device in a private network can choose the address that Awesonix Mail counts for it. Narrow the value if you do not trust every device in your private networks. The [deployment guide](../deployment/README.md#client-address-behind-a-proxy) describes the rule in full.

Administrators have no routine access to provider secrets or message content. This guide will describe such access only after an explicit, audited product policy is approved.

## Move settings to another installation

Each user can export and import their own settings under **Settings > Account > Security**. The export is an encrypted bundle.

To export or import settings:

1. Open **Settings > Account > Security**.
2. Confirm your current password.
3. Enter a separate backup passphrase of at least 12 characters.
4. Start the export or the import.

Both actions also use the session and CSRF protection.

### What the bundle includes

- Mailboxes and their credentials
- Identities and rules
- Interface, navigation, and notification preferences
- Tasks and boards
- Note sections, pages, notes, images, and the private-notes lock time; private notes are decrypted for the export and encrypted again on import
- AI provider settings (including each connection's context window and answer limit) and AI mailbox settings
- Vigil behaviour, briefing policies, and explicit memories
- Calendar and contact configuration
- Local-only mail, and active drafts with their attachments
- The avatar of the user and the photos of contacts that the user owns
- Portable defaults of the founding administrator

### What the bundle excludes

- Login hashes, TOTP data, and recovery material
- Sessions and audits
- DAV and companion credentials
- Notification endpoints
- The notes PIN, unlock sessions, page members, and public note links
- Installation keys
- Database and service configuration
- Downloaded provider mail and provider projections
- UI and Vigil state that is local to a device
- Generated briefings
- AI conversations, history, jobs, and suggestions
- Operational maintenance state

The architecture documentation has the authoritative list of included and excluded items.

### How import works

- Import changes the owner of the data to the destination user.
- Import encrypts the secrets again for the destination installation. You can repeat an import safely.
- Import is transactional and only merges. It adds or updates the sections in the bundle. Other sections stay unchanged.
- If an identifier collides, the import fails and changes nothing.
- Mail and AI provider credentials are decrypted only inside the trusted application process. The destination vault encrypts them again at once.
- A bundle declares its required features. An older version cannot silently drop a newer AI section.
- Settings that depend on folders keep the remote folder names. They link again after the folders of the destination synchronise.

Import suspends every imported mailbox, whatever its state at export. Before the owner or an administrator resumes provider access, check these items:

- The configuration
- The AI settings
- The provider endpoints
- The folder mappings
- The destinations of filters

If an import is rejected, see the [troubleshooting guide](../troubleshooting/README.md).

## Contacts capacity and recovery

### Contact photos

Contact photos are stored by content address under `/srv/mail/contact-photos`. This folder is inside the mail storage volume. The full recovery workflow covers this volume.

- Upload accepts only JPEG or PNG files of limited size.
- Awesonix Mail stores each photo as a PNG without metadata.
- If storage is full, the photo operation fails. The current photo of the contact stays unchanged.
- Photos that no contact uses are removed only after a safety interval and a final reference check.

### Contact search

Contact search uses a MariaDB index that Awesonix Mail can rebuild. Users can rebuild the index for their own contacts from Contacts maintenance. The status of the rebuild job stays available after a restart.

Normal database tables hold the authoritative data:

- Address books and contacts
- Labels and source references
- Merge redirects
- Calendar links

### Tested capacity

Awesonix Mail was tested with 25,000 contacts, 100 calendars, and 100,000 events on a disposable installation. This is a tested capacity point, not a promise of unlimited size. Before you go beyond it, monitor these items on your own hardware:

- Database latency
- Free storage
- Rebuild duration
