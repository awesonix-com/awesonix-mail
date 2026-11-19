# Unraid Installation

Awesonix Mail uses Unraid's native Docker-template workflow; Unraid does not natively require Docker Compose for this package.

1. The template pulls `ghcr.io/awesonix-com/awesonix-mail:latest` automatically. For an offline installation, verify and load `awesonix-mail-0.115.5-linux-amd64.tar` (download it from the [GitHub Release page](https://github.com/awesonix-com/awesonix-mail/releases/tag/v0.115.5)), then run `docker tag awesonix-mail:0.115.5 ghcr.io/awesonix-com/awesonix-mail:latest` in an Unraid terminal before applying the template.
2. Copy `my-awesonix-mail.xml` to `/boot/config/plugins/dockerMan/templates-user/`.
3. Provision a separate database container or server (tested with MariaDB 11.4) and a dedicated user. The database fields of the template are optional. Leave the server address and password empty to enter the database settings in the first-run wizard. Or enter the server address, port, database name, username, and password in the template; then the variables win. Do not use `127.0.0.1` for another container.
4. In the Unraid Docker tab, choose **Add Container**, select the Awesonix Mail template, and review the three Awesonix storage paths. They may be absent on a clean installation; the container initialises them automatically.
5. Apply the template and open `http://<unraid-host>:8180/setup`.

The default Awesonix host paths are under `/mnt/user/appdata/awesonix-mail/`. Preserve `mail`, `secrets`, and `logs` during every container replacement. If the wizard saved the database settings, `secrets/database.json` holds them: back it up with `master.key`. The template sets `--stop-timeout=120`; keep it. If the database step waits or fails, see the troubleshooting guide in the `docs` folder of this package. Back up and recover the database with the independently managed database service's tooling. Before you update to 0.108.0, back up the database: the update drops the tables of the old briefing method, and published briefings stay readable. If you added `AWESONIX_MAIL_AI_DIAGNOSTICS_DIR` or `AWESONIX_MAIL_BRIEFING_LEGACY_KINDS` as a variable, remove it.

## Diagnostics security defaults

The Unraid template exposes the relevant controls as advanced fields but defaults to a closed production configuration:

| Field | Safe default |
|---|---|
| Runtime Environment | `production` |
| Diagnostics Listener | `off` |
| Debug Log Port | empty/unpublished |
| Debug Token Minutes | `60` |
| Tokenless Local Diagnostics | `off` |
| Tokenless Diagnostics Owner | empty |
| Durable AI Analysis | `true` |
| Durable AI Briefings | `true` |
| AI Completion Notifications | `false` |

For ordinary production use, leave those values unchanged. To temporarily enable token-authenticated logs and MCP diagnostics, set **Diagnostics Listener** to `0.0.0.0:8182`, publish **Debug Log Port** `8182` only on a trusted network or through a TLS-protected proxy, apply the template, and issue an expiring observation or control token from the authenticated administrator interface. Return the listener to `off` and clear the port mapping after the diagnostic session.

Never enable tokenless local diagnostics on the production Unraid instance. Configuration validation rejects `observe` or `control` while **Runtime Environment** remains `production`, and it rejects an owner value while tokenless mode is `off`. The dual tokenless workflow belongs only to the isolated loopback synchronisation lab documented in `tooling/development/sync-lab/README.md` in the source repository.
