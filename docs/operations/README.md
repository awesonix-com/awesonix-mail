# Operations Guide

| **Last updated** | 2026-11-14 |
| **Scope** | External database, storage, backup, restore, portable settings, monitoring, companion devices, meeting mail, upgrades, the upgrade to version 0.110.0, the upgrade to version 0.111.0, the upgrade to version 0.112.0, and the upgrade to version 0.113.0 |

This guide is for the person who runs the Awesonix Mail server. It explains how to protect your data, how to back it up and restore it, and how to upgrade safely.

To install Awesonix Mail, follow the [installation guide](../installation/README.md). For the synchronisation worker, read the [synchronisation worker runbook](synchronization-worker.md).

In this guide:

1. [Who is responsible for what](#who-is-responsible-for-what)
2. [Data that you must protect](#data-that-you-must-protect)
3. [Back up your data](#back-up-your-data)
4. [Test a restore](#test-a-restore)
5. [Restore your data](#restore-your-data)
6. [Move user settings to a new installation](#move-user-settings-to-a-new-installation)
7. [Monitoring and recovery](#monitoring-and-recovery)
8. [Companion devices](#companion-devices)
9. [Meeting mail in mailboxes](#meeting-mail-in-mailboxes)
10. [Upgrade Awesonix Mail](#upgrade-awesonix-mail)

## Who is responsible for what

Awesonix Mail needs a database server that you operate separately. Awesonix Mail is tested with MariaDB 11.4. When Awesonix Mail starts, it does these things with the database:

- It checks the connection.
- It applies forward schema migrations.
- If the user may do so, the first-run wizard creates the empty database.

You, the operator, are responsible for everything else in the MariaDB service:

- availability and capacity
- access control and TLS
- upgrades of MariaDB
- logical backup, restore, and disaster recovery

### What the container contains

The application runtime does not run as root and has no shell. A small first-party bootstrap with limited privileges runs first. It prepares only the three storage mount roots. Then it permanently drops to the user `10001:10001`. This happens before migrations or application processes start.

The image does not include these items:

- MariaDB Server
- MariaDB client utilities
- dump tools

For this reason, run database tools such as `mariadb-dump` in the MariaDB container or on the MariaDB server.

The image includes the `recover-founder` command for a founding administrator who lost the password or the authenticator. The [administrator guide](../administrator-guide/README.md#recover-the-founding-administrator) explains it.

## Data that you must protect

Awesonix Mail keeps its data in four parts:

| Part | Contents | Back up |
|---|---|---|
| MariaDB database | The authoritative users, configuration, mail metadata, tasks, contacts, calendars, operations, and encrypted provider credentials | Required |
| `/srv/secrets` | The 32-byte installation master key that decrypts protected database values, and `database.json` when the first-run wizard saved the database settings | Required |
| `/srv/mail` | Retained raw mail objects, active draft attachments, contact photos, avatars, and the rebuildable search projection | Required |
| `/srv/logs` | Bounded diagnostic history; useful, but not authoritative product data | Recommended |

> **Warning:** A database backup without the matching `/srv/secrets` folder cannot be used. The master key in this folder decrypts the protected database values.

`database.json` is next to `master.key`. It is a plain file with mode `0600`. It holds the database host, port, name, user, and password that the wizard saved. Back it up with the rest of `/srv/secrets`, and protect the backup. If you set the database in the container settings, the file does not exist.

Follow these rules for every backup:

- Back up MariaDB, `/srv/secrets`, and `/srv/mail` together as one coordinated recovery generation. All parts of a recovery generation come from the same time.
- Store recovery copies outside the live host.
- Encrypt recovery copies at rest.
- Never publish database credentials, the master key, sessions, provider secrets, or raw message content.

### Where the parts are on your server

The paths `/srv/mail`, `/srv/secrets`, and `/srv/logs` are paths inside the container. This table shows the default locations on your server after the [installation guide](../installation/README.md). If you changed a path during installation, use your own path.

| Part | Unraid | Synology | Docker |
|---|---|---|---|
| Database | MariaDB appdata folder, for example `/mnt/cache/appdata/mariadb` | `data/database` in `/volume1/docker/awesonix-mail` | `data/database` in the `awesonix-mail` folder |
| `/srv/mail` | `/mnt/user/appdata/awesonix-mail/mail` | `data/mail` in the project folder | Docker volume `awesonix-mail` |
| `/srv/secrets` | `/mnt/user/appdata/awesonix-mail/secrets` | `data/secrets` in the project folder | Docker volume `awesonix-secrets` |
| `/srv/logs` | `/mnt/user/appdata/awesonix-mail/logs` | `data/logs` in the project folder | Docker volume `awesonix-logs` |

If your MariaDB server runs on another computer, use its documented logical or physical backup tooling.

## Back up your data

There are two ways to save the database:

- **Simple backup:** Stop MariaDB and copy its data folder.
- **Database export:** Keep MariaDB running and export the database with `mariadb-dump`.

In both ways, stop Awesonix Mail first. Then nothing changes while you copy, and the database and the storage come from the same time.

### Simple backup

Use this way if MariaDB runs in a container on the same server.

> **Warning:** Do not copy the MariaDB data folder while MariaDB runs. Such a copy can be unusable.

1. Stop the Awesonix Mail container.
2. Stop the MariaDB container.

   With Docker, run `docker compose stop` in the `awesonix-mail` folder. This command stops both containers. On Synology and Unraid, stop the containers in **Container Manager** or in the **Docker** tab.

3. Copy the database data folder to your backup location. Keep the file owners and permissions, for example with `sudo cp -a` or `rsync -a`.
4. Copy the `mail`, `secrets`, and `logs` storage to the same backup location.

   With Docker, the storage is in Docker volumes. Run these commands in the `awesonix-mail` folder to copy each volume into a `backup` folder:

   ```sh
   mkdir -p backup
   for v in awesonix-mail awesonix-secrets awesonix-logs; do
     docker run --rm -v "$v":/data:ro -v "$PWD/backup":/backup alpine:3.20 tar -czf "/backup/$v.tar.gz" -C /data .
   done
   ```

5. Start the MariaDB container. Wait until its log shows `ready for connections`.
6. Start the Awesonix Mail container.
7. Open `/health/ready`, for example `http://192.168.1.10:8180/health/ready`. Make sure that it succeeds.
8. Encrypt the backup and move it to a place outside the server.

On Unraid, the **Appdata Backup** app can do these steps on a schedule.

### Backup with a database export

Use this way if you prefer an export file, or if you cannot stop MariaDB. In the commands, replace `<mariadb-container>` with the name of your MariaDB container. The installation guide uses `awesonix-mail-database` on Synology and Docker.

1. Stop the Awesonix Mail container. Keep MariaDB running.
2. Export the database to a file inside the MariaDB container:

   ```sh
   docker exec -it <mariadb-container> mariadb-dump -u root -p --result-file=/tmp/awesonix_mail.sql awesonix_mail
   ```

   The command asks for the database root password. Enter it and press Enter.

3. Copy the file out of the container. Then delete the copy in the container, because it contains all your data:

   ```sh
   docker cp <mariadb-container>:/tmp/awesonix_mail.sql ./awesonix_mail.sql
   docker exec <mariadb-container> rm /tmp/awesonix_mail.sql
   ```

4. Copy the `mail`, `secrets`, and `logs` storage, as in step 4 of the [simple backup](#simple-backup).
5. Start the Awesonix Mail container.
6. Encrypt the export file and the storage copies. Move them to a place outside the server.

## Test a restore

Do not rely on a backup before you test it. Restore it into a separate, empty MariaDB instance. A valid test proves all of these points:

1. The restored database is reachable with a dedicated least-privilege application account.
2. The matching secrets and mail volumes are mounted read/write for the runtime user `10001:10001`.
3. Startup migrations complete without a dirty schema.
4. `/health/ready` succeeds, and both the API and the synchronisation worker remain running.
5. Content-integrity diagnostics and representative user journeys pass.

## Restore your data

> **Warning:** Do not run schema down-migrations against the only copy of production data. Pre-1.0 database downgrade and embedded-database compatibility are not supported.

> **Warning:** Never start an Awesonix Mail version that is older than the restored database schema. An old runtime does not work with a new schema.

1. Stop the Awesonix Mail container.
2. Before you replace anything, copy the current database and storage to a safe place. You may need them if the restore fails.
3. Restore the database from your backup. Choose the way that matches your backup:

   - **Data folder copy:** Stop MariaDB. Replace its data folder with the copy from the backup, and keep the file owners. Start MariaDB and wait for `ready for connections`.
   - **Export file:** Create an empty `awesonix_mail` database and the `awesonix` user, as in [Step 2: Create the database](../installation/2-database.md). Then import the file:

     ```sh
     docker cp awesonix_mail.sql <mariadb-container>:/tmp/awesonix_mail.sql
     docker exec -it <mariadb-container> mariadb -u root -p awesonix_mail -e "source /tmp/awesonix_mail.sql"
     docker exec <mariadb-container> rm /tmp/awesonix_mail.sql
     ```

     The second command asks for the database root password.

4. Restore the `mail`, `secrets`, and `logs` storage from the same backup.

   With Docker volumes, run these commands in the folder that contains `backup`:

   ```sh
   for v in awesonix-mail awesonix-secrets awesonix-logs; do
     docker run --rm -v "$v":/data -v "$PWD/backup":/backup alpine:3.20 tar -xzf "/backup/$v.tar.gz" -C /data
   done
   ```

5. Check that the storage belongs to the user `10001:10001` and is writable. If it is not, change the owner, for example with `sudo chown -R 10001:10001 <folder>`.
6. If you used the wizard for the database, the restored `secrets` folder must contain `database.json`. Without it, or without the database variables, the set-up stays locked. Put the file or the variables back before you start the container.
7. Start the Awesonix Mail container. Startup applies the pending forward migrations.
8. Check the result:

   - **Settings > Diagnostics** reports a clean schema and passing content integrity.
   - `/health/ready` succeeds.
   - The API and the synchronisation worker remain running.
   - Representative user journeys work, for example sign-in, reading mail, and search.

If you restored to a different server identity, the companion devices must pair again. Read [Companion devices](#companion-devices).

## Move user settings to a new installation

Each signed-in user can create a passphrase-encrypted settings bundle under **Settings > Account > Security**. Use this bundle to move user-owned configuration to a clean installation.

1. On the old installation, each user creates a bundle.
2. On the new installation, complete the first-run setup.
3. Each user signs in and imports the bundle. The [user guide](../user-guide/README.md) explains the import.

The bundle includes these items:

- mailboxes and encrypted credentials
- identities, rules, and preferences
- tasks and boards
- calendar and contact configuration
- local-only mail, active drafts, and attachments
- avatars and owned-contact photos
- the portable defaults of the founding administrator

The bundle intentionally excludes these items:

- login passwords and hashes, TOTP, and recovery material
- sessions and audits
- DAV device credentials and installation keys
- database configuration
- downloaded provider mail and provider-derived projections
- AI history and suggestions
- operational jobs

### Vigil data in the bundle

Vigil profiles, standing instructions, and briefing policies are part of the settings bundle. Device states, pinned contexts, generated briefing history, and runtime cursors stay as installation-side database records.

Daily, activity, weekly, and monthly policies are disabled by default for new users. The AI posture of the user alone decides whether a briefing can use a cloud provider. Awesonix Mail automatically limits terminal briefing records to twelve months and the latest 100 per owner.

## Monitoring and recovery

### Health checks

- `/health/live` reports process liveness.
- `/health/ready` succeeds only when the application and the database-backed runtime are ready. During the first database build, the container health state is `starting`.
- **Settings > Diagnostics** reports schema, worker leadership, mailbox state, durable queue pressure, storage capacity, and content integrity. It does not show message content or secrets.

### What to do when something fails

- **Database unavailable:** Treat this as a deployment incident and restore connectivity. At container startup, Awesonix Mail waits for the database and retries with a growing delay. It does not stop. The health state stays `starting` for up to 60 minutes. See the [troubleshooting guide](../troubleshooting/README.md) for the recovery paths.
- **Dirty schema:** The container was killed during a migration step. The status shows `schema_dirty` with a version. This is the one place where manual SQL is needed. A stop grace period of 120 seconds, as in the templates, avoids it. Do not stop or restart the database server during a build: this also causes it. Use the same procedure as the [troubleshooting guide](../troubleshooting/README.md#a-dirty-schema):
  1. Stop the container.
  2. The safest way is to restore the database backup that you made before the upgrade.
  3. The advanced way needs care.
     1. Find the migration file for the dirty version. In the image, the files are in `/srv/migrations` as `<version>_<name>.up.sql`. The image has no shell, so copy the file out with `docker cp <container>:/srv/migrations/<version>_<name>.up.sql ./`. This works on the stopped container. Or read the same file in the product source, in `product/database/migrations`.
     2. Read its statements. Some statements may have run, or all of them, because a database does not undo changes to its structure when a step stops. Compare the file with the database. Use `SHOW TABLES`, `SHOW COLUMNS FROM <table>`, `SHOW INDEX FROM <table>`, `SHOW TRIGGERS`, and `SHOW FULL TABLES WHERE Table_type = 'VIEW'` for the objects that the file creates.
     3. Undo the partial effects. Drop only the tables, columns, indexes, triggers, or views that the file created. The schema must equal its state before that migration.
     4. Set the version back and clear the flag. Replace `<dirty version minus one>` with the number: `UPDATE schema_migrations SET version = <dirty version minus one>, dirty = 0;`
     5. Never mark a half-applied migration as done.
  4. Start the container. Startup runs the migration file again from its start.
  5. If you are not sure, restore the backup instead.

  [Upgrade to version 0.110.0](#upgrade-to-version-01100) shows a worked case for migration 000192.
- **Search problems:** Awesonix Mail can rebuild search from the authoritative MariaDB metadata and the retained content. Use the repair action in the product. Do not delete files or rows manually.
- **Message and folder actions:** These actions are durable and restart-safe. Diagnose authentication, network, provider, and storage failures before you change the queue state.
- **Notifications:** Events, centre items, schedules, deliveries, and encrypted endpoints are authoritative MariaDB state. Use the administrator's self-only delivery test and the aggregate health view. Do not inspect user text or delete queue rows to force delivery.
- **Vigil briefing backlog:** After a restart, Vigil reuses unfinished Mail Activity obligations. It combines several missed Daily cutoffs into one catch-up record. Do not delete briefing or policy rows to suppress a backlog. Instead, disable the policy in the AI behaviour settings and investigate provider availability or consent failures.
- **Vigil briefing data:** A briefing run stores mail identities and chunk state, never mail text. Before you update to 0.108.0, back up the database. Migration 000190 drops the tables of the old briefing method and has no data restore.
- **Meeting mail:** Invitations, answers, the cleanup of Sent copies, and the move of answer mail to Archive are durable work. They continue after a restart. Do not delete meeting, submission, or operation rows to stop or speed up this work.

When mail moves to Bin or is permanently deleted, Awesonix Mail intentionally dismisses the related notification items. It also suppresses their unfinished deliveries.

### Settings for durable AI work

These environment variables configure AI work:

| Variable | Default | Reason |
|---|---|---|
| `AWESONIX_MAIL_AI_DURABLE_ANALYSIS` | on | The user interface for analysis requires the durable work APIs. |
| `AWESONIX_MAIL_AI_DURABLE_BRIEFINGS` | on | Compatibility setting. Both values retain the chunk state that the briefing engine needs. |
| `AWESONIX_MAIL_AI_COMPLETION_NOTIFICATIONS` | on | A ready Vigil briefing sends an Assistant notification. Users can turn it off in the notification settings. |

Disable `AWESONIX_MAIL_AI_DURABLE_ANALYSIS` to use bounded analysis without the durable analysis worker. This setting is independent of briefing processing. The deprecated `AWESONIX_MAIL_AI_DURABLE_BRIEFINGS` setting cannot disable briefing chunk state. Disable the relevant briefing policies to stop scheduled briefing work.

Analysis claims, briefing snapshots and chunk state, and assistant context evidence are authoritative database state. Include them in the coordinated backup.

## Companion devices

Users approve and revoke Android and iOS devices under **Settings > Account > Device access**.

- Pairing requests are short-lived and can be used only once.
- Device credentials rotate.
- Device credentials are separate from browser sessions, DAV credentials, login passwords, and provider credentials.

Treat these events as security events: unexpected pairing attempts, credential replay, or repeated rotation failures. Review the companion audit entries before you change data directly.

### Revoke a device

Revocation becomes authoritative when the device reconnects. The server then rejects later companion requests. After the app confirms the revocation, it deletes these items:

- the authorisation
- the encrypted Mail, Contacts, Calendar, and Tasks projections
- pending commands and conflict drafts

A device that stays offline may still show content that it kept before revocation. If immediate offline erasure is required, advise users to apply a remote wipe from their device management.

### Devices and backups

Backups keep the server-side device and audit records in MariaDB. A backup never rebuilds the device key or the encrypted projection of an app. After a restore to a different server identity, the devices must pair again.

- Do not copy companion credentials between devices.
- Do not copy device-local SQLCipher files between devices.

Before release distribution, complete qualification for physical-device backup and transfer, production TLS, signing, privacy, and export control.

## Meeting mail in mailboxes

Calendar meetings make Awesonix Mail change provider mailboxes by itself, without a click of the user. It does this only in interactive mailboxes. A mirror-only mailbox is never changed, and it cannot send meeting mail.

- **Sent copies:** Some providers, such as Gmail, file their own copy of each sent mail in Sent. Awesonix Mail moves its own meeting mail from Sent to Bin. It moves only a copy that carries the exact message ID and a marker that only this server can create. It looks for copies during 7 days after the send, and it never deletes them permanently.
- **Answer mail:** When a user turns off **Get an email when guests respond**, applied answers move from the Inbox to Archive. Without an Archive folder, the mail stays in the Inbox.
- **Received invitations:** Invitations, updates, and cancellations from other organisers change the user's calendar. They send no mail. Only an answer of the user sends mail.

Awesonix Mail sends meeting mail with the normal mail retries. A mail fails when the provider rejects it permanently, or when it is still not accepted after about 4.6 days. The guest then shows a warning. Mail that may have arrived is never sent again.

## Upgrade Awesonix Mail

1. If the upgrade is a breaking pre-1.0 upgrade, export the portable settings first. Read [Move user settings to a new installation](#move-user-settings-to-a-new-installation).
2. Make a backup and verify it. You need an external-database backup and the matching `secrets` and `mail` storage. Read [Back up your data](#back-up-your-data).
3. Pull the new image and recreate only the application container. Do not recreate the MariaDB container.

   With Docker, run these commands in the `awesonix-mail` folder:

   ```sh
   docker compose pull awesonix-mail
   docker compose up -d awesonix-mail
   ```

4. Wait for the startup. Startup applies the pending forward migrations.
5. Check health, content integrity, search, and representative user journeys.
6. Keep the prior image and the pre-upgrade recovery generation until all checks pass.

> **Warning:** After users create or receive meetings, the database refuses to roll back the meeting schema. Restore the pre-upgrade backup instead. Mail that was already sent cannot be recalled.

If the upgrade fails, stop the new version. Then restore the matching database and storage from the pre-upgrade recovery generation. Read [Restore your data](#restore-your-data) and the rollback rules in the [synchronisation worker runbook](synchronization-worker.md#roll-back-an-upgrade).

### Upgrade to version 0.110.0

Version 0.110.0 adds language packs, the setting My language, and the setting Filter languages. It applies three migrations: 000192, 000193, and 000194.

Do step 1 before the new image reaches a server whose database you have not read. Steps 3 and 4 are optional. Step 5 matters only if you must go back.

**1. Check the stored interface languages before the upgrade.**

Migration 000192 replaces the database check on the stored interface language. The old check accepted a code in any letter case, for example `EN` or `Hu`. The new check accepts lower-case codes only. A stored `EN` makes the migration fail with error 4025. The schema then stays dirty, and the application refuses to start.

The application itself writes lower-case codes only, so a hit is rare. Run this read-only query first:

```sh
docker exec -it <mariadb-container> mariadb -u root -p awesonix_mail -e "
SELECT owner_user_id, language_mode, interface_language, HEX(interface_language) AS hex_value
  FROM user_interface_preferences
 WHERE language_mode = 'explicit'
   AND NOT (CHAR_LENGTH(interface_language) BETWEEN 2 AND 8
            AND CAST(interface_language AS BINARY) REGEXP '^[a-z]{2,3}(-[A-Za-z0-9]{2,4})?\$');"
```

No row means that you can upgrade. For each row, make the backup of [Upgrade Awesonix Mail](#upgrade-awesonix-mail) first. Then write the code in lower case. The higher revision makes a client with an old copy see a conflict and reload:

```sh
docker exec -it <mariadb-container> mariadb -u root -p awesonix_mail -e "
UPDATE user_interface_preferences
   SET interface_language = LOWER(interface_language), revision = revision + 1, updated_at = UTC_TIMESTAMP(6)
 WHERE owner_user_id = '<owner_user_id from the query>';"
```

Run the query again. It must return no row. Rehearsed: the query, the fix, and the failure of the migration on a stored `EN` ran on a disposable MariaDB 11.4.12 copy of the table. Not run: the query against a real server database.

**If you ran the upgrade before the check and migration 000192 failed.**

Rehearsed on a disposable copy of the database (MariaDB 11.4.12) with the command `migrate up`. Not run on a real server. The container start after the fix was not rehearsed.

Migration 000192 is one `ALTER TABLE` statement, so a failed run changes no data. The old check stays in place. Only the table `schema_migrations` changes: it holds version 192 and `dirty` equal to 1. The command `migrate up` then answers "Dirty database version 192". The project has no force command. Continue in this order:

1. Stop the Awesonix Mail container. Leave the database running.
2. Make the backup of [Upgrade Awesonix Mail](#upgrade-awesonix-mail).
3. Run the query and the fix above until the query returns no row.
4. Set the version back to 191 and clear the dirty flag:

   ```sh
   docker exec -it <mariadb-container> mariadb -u root -p awesonix_mail -e "UPDATE schema_migrations SET version = 191, dirty = 0;"
   ```

5. Start the Awesonix Mail container. Startup runs the migrations again.
6. Read the version. It must show 194 and `dirty` equal to 0:

   ```sh
   docker exec -it <mariadb-container> mariadb -u root -p awesonix_mail -e "SELECT version, dirty FROM schema_migrations;"
   ```

If you prefer, restore the backup that you made before the upgrade instead.

**2. Upgrade as usual.**

Follow [Upgrade Awesonix Mail](#upgrade-awesonix-mail). Startup applies the three migrations. Existing accounts keep their languages. My language is empty for them, and the first proposal counts as done. The Filter languages of each account start as the five shipped languages.

**3. Convert old filter rules (optional).**

A rule keeps its own words until you convert it. A converted rule refers to a keyword group, so it follows later keyword reviews and later languages. The command proves for each rule that it matches the same words as before. The apply works on one account per run:

```sh
docker compose -f product/deployment/container/docker-compose.yml -p awesonix-mail run --rm --no-deps -T \
  --entrypoint /usr/local/bin/awesonix-mail awesonix-mail rules-groups convert [--owner <email>] > report.json
docker compose -f product/deployment/container/docker-compose.yml -p awesonix-mail run --rm --no-deps -T \
  --entrypoint /usr/local/bin/awesonix-mail awesonix-mail rules-groups convert --apply --yes --owner <email>
```

Without `--apply`, the command is a dry run. It reads and writes nothing and prints one JSON report. With `--apply`, you must give `--yes` and `--owner <email>`. Replace `-f` and `-p` with the compose file and the project name of your server. Use `-T`, so that standard output holds only the JSON report.

The command stops when a pending arrival holds a group term. It also stops when the account's filter languages do not hold every language that ships keyword lists. Exit code 0 means done. Exit code 1 means refused or an error. Exit code 2 means that a rule failed to write.

Rehearsed: the dry run and the apply in this compose form on the test stack, for two test accounts.

**4. Write the words back before a rollback.**

The command `rules-groups reverse` writes the words of every group reference back into the rules. An older build can then read them. It uses the account's effective filter languages, so it never changes which mail a rule matches. It has the same flags as `convert`, and it gives a dry run unless you add `--apply --yes --owner <email>`:

```sh
docker compose -f product/deployment/container/docker-compose.yml -p awesonix-mail run --rm --no-deps -T \
  --entrypoint /usr/local/bin/awesonix-mail awesonix-mail rules-groups reverse --apply --yes --owner <email>
```

A live rule over 400 words or 8,192 bytes is refused with the status `refused-limit`. Version 0.112.0 raised this limit from 200 words and 4,096 bytes. Edit or delete that rule, or restore the backup.

Exit code 3 means that rows stay at rule schema 4. Rehearsed: the dry run and the apply on a copy of the database, started with `docker run` and the same entrypoint. Not rehearsed: the compose form of `reverse` on the test stack.

**5. Roll back the upgrade.**

The default rollback is a restore of the backup that you made before the upgrade. Follow [Restore your data](#restore-your-data). You lose all data written after the backup. An older image alone is not enough, because the schema is now 194. Never restore a dump of a newer schema into an older application.

The down path keeps newer data, but it drops three things. Migration 000194 drops the Filter languages of every account. Migration 000193 drops My language and the proposal state. Migration 000192 sets an explicit interface language outside en, hu, de, nl, and fr back to Automatic, and it raises the revision. Use the down path only when you accept these losses:

1. Stop the application container. Leave the database running.
2. Make a dump of the current database, and test it with `gzip -t`. The warning in [Restore your data](#restore-your-data) still applies: this dump is your safety copy.
3. Run `rules-groups reverse` as in step 4, one run per account that has converted rules. Then check that no rule has rule schema 4 or higher. The query must return 0:

   ```sh
   docker exec -it <mariadb-container> mariadb -u root -p awesonix_mail -e "SELECT COUNT(*) FROM local_mail_filter_rules WHERE condition_schema_version >= 4;"
   ```

4. From schema 195, run `migrate down-one` once first, as the section on version 0.111.0 describes. Then run it three times from the new image, because the old image has no down files. The steps go from 194 to 193, 192, and 191. After each step, read the version, which must be the expected number with `dirty` equal to 0:

   ```sh
   docker compose -f product/deployment/container/docker-compose.yml -p awesonix-mail run --rm --no-deps -T \
     --entrypoint /usr/local/bin/awesonix-mail awesonix-mail migrate down-one
   docker exec -it <mariadb-container> mariadb -u root -p awesonix_mail -e "SELECT version, dirty FROM schema_migrations;"
   ```

   The guard refuses the first two steps while a rule holds a keyword group. The command then exits with 1, and the version does not change. Run `rules-groups reverse` and try again.
5. Start the image of the previous version, which expects schema 191. Check `/health/ready`, the schema version, and the log. Check that a test account loads its preferences and rules.

Exports: a version older than 0.110.0 cannot import an export from the new version. Every new export declares the language features, even without rules or groups. The older server refuses the whole file.

Make the exports that you need with the older version before you roll back. Keep an export of the new version only for a later upgrade. The refusal comes from the importer code, and nobody has tried it on a real older server.

Rehearsed on copies of the database: the restore path and the guard refusal. Also `reverse` followed by `migrate down-one` from 194 to 192, started with `docker run` and the same entrypoint. Also the step from 192 to 191, with the development launcher.

Not rehearsed: the compose form of `migrate down-one`, and the three steps in one run. Nobody has rehearsed the rollback on a real server yet.

### Upgrade to version 0.111.0

Version 0.111.0 renames the five first language packs to regional codes. The codes `en`, `hu`, `de`, `nl`, and `fr` become `en-US`, `hu-HU`, `de-DE`, `nl-NL`, and `fr-FR`. The upgrade applies migration 000195. Deploy the server first, and update the phone app after it.

**1. Upgrade the server.**

Follow [Upgrade Awesonix Mail](#upgrade-awesonix-mail), including the backup. Startup applies migration 000195. It rewrites the stored short codes `en`, `hu`, `de`, `nl`, and `fr` to the regional codes in five places:

- the interface language of each account;
- My language;
- the AI answer language;
- the language of each calendar meeting review;
- the lists of filter languages.

The migration also removes the default value of the column `review_locale`. The application writes that value in every insert. No stored choice resets to Automatic. After the startup, read the schema version. It must show 195 and `dirty` equal to 0:

```sh
docker exec -it <mariadb-container> mariadb -u root -p awesonix_mail -e "SELECT version, dirty FROM schema_migrations;"
```

Rehearsed: migration 000195 ran up, down, and with its guard on a disposable MariaDB 11.4.12 copy, in the language-settings lane of the repository database tests. That test does not store meeting review languages. Not run: the migration on a real server database.

**2. Update the phone app after the server.**

Update the phone app only when the server runs version 0.111.0. A phone app of version 0.110.x still sends the short codes to the new server. The server resolves a bare language such as `hu` to the one pack of that language and stores `hu-HU`. A queued change of an old app is accepted the same way, and no language resets.

Nobody has tested an old phone app against the new server on a device.

**3. Old settings backups still restore.**

A settings backup from an earlier version holds short codes. The import resolves them in the same way. It restores the interface language, My language, the AI answer language, and the filter languages. It drops only a code that no pack has, and it warns with that code.

An export of the new version holds regional codes. An older server does not know them, so by its import rule it drops them with a warning. Import such an export only into the new version or later.

**4. Roll back the upgrade.**

The default rollback is a restore of the backup that you made before the upgrade. Follow [Restore your data](#restore-your-data). You lose all data written after the backup.

The down path keeps newer data. Migration 000195 down moves the five regional codes back to the short codes in the same five places. It also gives the column `review_locale` its old default again. The step is exact: it changes only these five pairs. Use it in this order:

1. Stop the application container. Leave the database running.
2. Make a dump of the current database, and test it with `gzip -t`.
3. Run `migrate down-one` once from the new image. The old image has no down files. The version goes from 195 to 194:

   ```sh
   docker compose -f product/deployment/container/docker-compose.yml -p awesonix-mail run --rm --no-deps -T \
     --entrypoint /usr/local/bin/awesonix-mail awesonix-mail migrate down-one
   docker exec -it <mariadb-container> mariadb -u root -p awesonix_mail -e "SELECT version, dirty FROM schema_migrations;"
   ```

4. Start the image of the previous version, which expects schema 194. Check `/health/ready`, the schema version, and the log. Check that a test account keeps its interface language.

The guard refuses the down step while a stored value is a language code without a short form. Such a code appears when an account chose a language that was added after the upgrade. The command exits with 1 and the version does not change. The message names the place and the number of values. Change those values first, or restore the backup instead. Without the guard, the older version would read such a value as an unknown language.

Rehearsed: the guard refusal and the down step on the disposable copy named in step 1. Not rehearsed: the compose form of `migrate down-one`, and a rollback on a real server.

### Upgrade to version 0.112.0

Version 0.112.0 adds eight languages: Japanese (`ja-JP`), Korean (`ko-KR`), Chinese Simplified (`zh-CN`), Russian (`ru-RU`), Ukrainian (`uk-UA`), Polish (`pl-PL`), Spanish (`es-ES`), and Italian (`it-IT`). They appear in the language menus of the web app and the phone app. The update adds no database migration.

1. Follow [Upgrade Awesonix Mail](#upgrade-awesonix-mail), including the backup. Deploy the server first, and update the phone app after it.
2. Existing accounts keep their languages. The Filter languages of an existing account do not change, so the new languages do not join a stored list.
3. Roll back with the previous image. Restore the backup when you also want to remove data written after the upgrade. No migration needs an undo step.
4. Before you roll back to a version before 0.112.0, check the rules with long word lists. See "Word-list limit" below.
5. Before you roll back to a version before 0.111.0, read step 4 of the section above. The guard of migration 000195 refuses the down step while a stored value is a language code without a short form. An account that chose one of the eight new languages has such a value. Reset the language of that account first.

The eight languages have no native review. Their keyword lists are drafts. The team ran no device test for them. The repository file `docs/development/localization-candidates.md` lists these doubts.

**Word-list limit.** The upgrade raises the limit of a has-any word list in a filter rule to 400 words and 8,192 bytes. The earlier limit was 200 words and 4,096 bytes. With 13 languages, the full expansion of one keyword group holds up to 237 words. Rule-group conversion and reversal failed above 200 words for accounts in the filter mode "All languages".

The web request body limit for a rule is now 256 KiB. No migration is needed, because rules are stored as JSON in the column `condition_json`.

A rollback to a build older than 0.112.0 needs care:

- The down guard of migrations 000193 and 000194 counts only rules at condition schema 4 or higher. It does not block a plain list of 201 to 400 words.
- An older build refuses to save such a rule, and the older phone app drops it. The older incoming path still matches subject lists.
- The command `rules-groups reverse` does not shorten lists. It writes up to 400 words. Its promise that an older build can read the rules holds only up to 200 words.

Before you roll back to a build older than 0.112.0, find rules with a list of more than 200 words or 4,096 bytes. Open each such rule on the web and shorten the list, or delete the rule.

A rule with several long lists can exceed the 60 KiB that the phone sends in one command. The phone then shows "too large, edit on the web". This happens from about eight full lists. Edit that rule on the web.

The test `TestLargestKeywordGroupExpansionFitsTheListLimits` fails when a future language makes a keyword group longer than the limit.

The frozen phone copies of contract 2.1 keep 200 words and 4,096 bytes on purpose.

### Upgrade to version 0.113.0

Version 0.113.0 adds British English (`en-GB`) and makes it the source language. The language menus list "English" for British English and "English (United States)" for American English. The update adds no database migration.

1. Follow [Upgrade Awesonix Mail](#upgrade-awesonix-mail), including the backup. Deploy the server first, and update the phone app after it.
2. Check the language of a test account that chose "English (United States)". It must still show American spelling, for example "Bin" and "Colour".
3. Roll back with the previous image. Restore the backup when you also want to remove data written after the upgrade. No migration needs an undo step.

**Existing accounts.** An explicit language choice never changes.

- An account that chose English before 0.111.0 holds `en-US` since migration 000195. It stays `en-US`, with the same American texts. The same holds for a stored value in the web browser, in the phone app, and in a settings backup from 0.111.x.
- An account that chose "Automatic" follows the browser language, as before. The browser tag now finds its pack by a new rule. See the table below.
- A bare `en` means the source language, which is now `en-GB`. This covers an old settings backup and a phone build from before 0.111.0 that wrote `en`.
- A stored value that names a region without its own pack, such as `en-AU`, is not changed. It reads as a language that does not ship, as before.

**Automatic accounts.** The result depends on the browser or phone language:

| Language of the browser or phone | Language that the app shows |
|---|---|
| `en-US` | American English (`en-US`) |
| `en-GB` | British English (`en-GB`) |
| `en`, `en-AU`, `en-CA`, `en-IN`, or any other English tag with no pack of its own | British English (`en-GB`) |
| A language with no pack | British English (`en-GB`), the source language |
| A language with a pack, such as `de-AT` | The pack of that language, as before |

An Automatic account with an `en-AU`, `en-CA`, or `en-IN` browser used to get American English. It now gets British English. To keep such a browser on American English, list its tag in `matchTags` of the pack `en-US` and build the image again. This is a data change, with no code edit.

**Server texts.** Some server texts follow the source language when the account has no other choice. They are the AI answer language "App language", the briefing, the meeting mail, and the share page. For an Automatic account that was on American English, these texts now use British spelling. An account with an explicit `en-US` keeps American texts.

**Keyword lists.** The pack `en-GB` holds the English keyword lists with one more word, `licence`, in the kind `orders`. The filter preset "My language + English" now holds both English lists. A custom list that holds `en-US` stays as saved and does not get `licence`. A rule-group conversion proof compares the whole expansion, so it can leave an old rule as a word list. Such a rule keeps its own words.

**Phone.** The phone follows after the new build. The default Android channel name reads "Synchronisation" until the app sends its text table.

**Rollback.** A build older than 0.113.0 does not know `en-GB`.

- A stored `en-GB` reads as Automatic. Nothing is lost.
- A settings backup that holds `en-GB` imports into an older build with a warning, and the language drops.
- This update runs no migration, so no data step is needed. Read the section for 0.111.0 when you roll back past it.
