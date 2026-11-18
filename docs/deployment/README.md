# Deployment reference

This page lists the container settings, storage paths, ports, and database tuning for Awesonix Mail. Use it to look up a setting after you install. If you install for the first time, follow the [installation guide](../installation/README.md) first.

## Application image

Awesonix Mail ships as one application image. The image runs as a non-root user and contains no shell. You must run a database server as a separate service that you manage yourself. Awesonix Mail is tested with MariaDB 11.4.

The image contains these items:

- the statically linked Go services
- the built web frontend
- the schema migrations
- the CA trust data and the time-zone data

The image does not contain MariaDB Server, the MariaDB client tools, Alpine Linux, BusyBox, or dynamically linked system libraries.

## Database connection

Run a database server and a dedicated user for Awesonix Mail. The user must be able to create and alter objects in its database. The database itself is optional: the setup wizard creates it when the user may do so. Use MariaDB 11.4 or newer.

Enter the settings in the first step of the [setup wizard](#first-run-setup), or set these variables on the application container. All of them are optional in the templates. An empty value counts as not set.

| Variable | Default | Meaning |
|---|---|---|
| `AWESONIX_MAIL_DATABASE_HOST` | Empty | Hostname or IP address of the database server, without a protocol or port |
| `AWESONIX_MAIL_DATABASE_PORT` | `3306` | TCP port of the database server, from 1 to 65535 |
| `AWESONIX_MAIL_DATABASE_NAME` | `awesonix_mail` in the templates | Database reserved for Awesonix Mail |
| `AWESONIX_MAIL_DATABASE_USER` | `awesonix` in the templates | Database account with full access to that database |
| `AWESONIX_MAIL_DATABASE_PASSWORD` | Empty | Password for the database account |

Follow these rules:

- If the host and the password are set, the variables always win. The wizard then shows the database step read-only. To change a rotated password, change the variables. Set both or neither. If only one is set, the set-up stops and names the missing variable.
- If the database runs in another container, use its service name as the host. Do not use `127.0.0.1`. Protect the password as a secret.

At startup, Awesonix Mail does these steps in order:

1. It answers on port 8180 from the first second. The health state is `starting` until the build ends.
2. It validates the database settings, from the variables or from the wizard. If the database is not reachable, it waits and retries with a growing delay. It does not stop.
3. It applies the forward migrations step by step. The log shows `database: step n of m ...` lines and a heartbeat every 30 seconds.
4. It starts the API and the synchronisation worker.

The wizard saves its values as `database.json` in `/srv/secrets`, next to `master.key`. The file is plain JSON with mode `0600`. See the [operations guide](../operations/README.md) for backup and the [troubleshooting guide](../troubleshooting/README.md) for recovery.

## Connection pool

The API and the synchronisation worker each have their own connection pool. The defaults suit a single installation. Together, they stay well below the default `max_connections` value of MariaDB. If a value is not valid, Awesonix Mail logs `database-pool: invalid <name>; using <default>` and keeps the default.

| Variable | Default | Meaning |
|---|---|---|
| `AWESONIX_MAIL_DATABASE_API_MAX_OPEN` | `32` | Maximum open connections for the API, from 1 to 500 |
| `AWESONIX_MAIL_DATABASE_API_MAX_IDLE` | `16` | Maximum idle connections for the API, from 0 to 500 |
| `AWESONIX_MAIL_DATABASE_WORKER_MAX_OPEN` | `16` | Maximum open connections for the worker, from 1 to 500 |
| `AWESONIX_MAIL_DATABASE_WORKER_MAX_IDLE` | `8` | Maximum idle connections for the worker, from 0 to 500 |
| `AWESONIX_MAIL_DATABASE_CONNECTION_LIFETIME_MINUTES` | `30` | Maximum age of a connection in minutes, from 1 to 1440 |
| `AWESONIX_MAIL_DATABASE_CONNECTION_IDLE_MINUTES` | `5` | Idle time in minutes before a connection closes, from 1 to 1440. Keep it below the `wait_timeout` value of the server. |

If a maximum for idle connections is higher than the maximum for open connections, Awesonix Mail uses the maximum for open connections.

## MariaDB tuning

We recommend these MariaDB settings:

| Setting | Recommendation |
|---|---|
| `innodb_buffer_pool_size` | Size it so that the Awesonix Mail database fits in memory. For example, use `1G` for a database of a few hundred MiB. |
| `tmp_table_size` | Raise it to `64M`. |
| `max_heap_table_size` | Raise it to `64M`. |
| `innodb_autoinc_lock_mode` | Keep it at `1`, the MariaDB default. Mobile synchronisation relies on this value to never skip a change. |

If `innodb_autoinc_lock_mode` is `2`, Awesonix Mail logs this line at startup: `companion-journal: autoinc_lock_mode=2 commit_fence=inexact`.

Warning: MariaDB silently ignores a configuration file that is world-writable. Its log then shows `Warning: World-writable config file … is ignored`. Keep your custom configuration files at mode `644`.

Store the MariaDB data on SSD storage. On Unraid, keep database flushes out of the `/mnt/user` FUSE layer. Do one of these:

- Map the database container directly to the pool path, for example `/mnt/<pool>/appdata/mariadb`.
- Enable exclusive shares.

## Storage paths

| Container path | Contents | Back up |
|---|---|---|
| `/srv/mail` | Retained raw mail, local and draft objects, photos, avatars, and the search projection | Yes |
| `/srv/secrets` | The master encryption key of the installation | Yes, with separate protection |
| `/srv/logs` | Bounded diagnostic history | Recommended |

The templates map these paths to the host as follows:

| Template | `/srv/mail` | `/srv/secrets` | `/srv/logs` |
|---|---|---|---|
| Docker | Volume `awesonix-mail` | Volume `awesonix-secrets` | Volume `awesonix-logs` |
| Synology | `./data/mail` | `./data/secrets` | `./data/logs` |
| Unraid | `/mnt/user/appdata/awesonix-mail/mail` | `/mnt/user/appdata/awesonix-mail/secrets` | `/mnt/user/appdata/awesonix-mail/logs` |

The image sets these paths inside the container:

| Variable | Value in the image |
|---|---|
| `AWESONIX_MAIL_CONTENT_PATH` | `/srv/mail/objects` |
| `AWESONIX_MAIL_SEARCH_PATH` | `/srv/mail/search` |
| `AWESONIX_MAIL_MASTER_KEY_PATH` | `/srv/secrets/master.key` |
| `AWESONIX_MAIL_DEBUG_LOG_FILE` | `/srv/logs/debug.log` |

### Ownership and privileges

The container runs its own storage bootstrap before migrations or application processes start. During the bootstrap, it uses only the `CHOWN`, `SETGID`, and `SETUID` capabilities. The bootstrap creates or adopts the three mount roots and assigns them to `10001:10001`. Then the container permanently drops its privileges. The root filesystem stays read-only.

You do not need to create fresh bind-mount directories in advance. You also do not need to change their ownership.

### Database files

Do not mount database files into the application container. The database operator owns database backup, restore, lifecycle, TLS, availability, and disaster recovery. Coordinate each database backup with matching generations of `/srv/secrets` and `/srv/mail`. `/srv/secrets` holds `database.json` when the wizard saved the settings.

## Container options

The templates start the container with these options:

| Option | Value |
|---|---|
| Root filesystem | Read-only |
| Security option | `no-new-privileges:true` |
| Capabilities | Drop `ALL`, then add `CHOWN`, `SETGID`, and `SETUID` |
| Memory limit | `2g` |
| CPU limit | `2` (Unraid; optional in Docker and TrueNAS; none in Synology) |
| Process limit | `512` (Unraid; optional in Docker and TrueNAS; none in Synology) |
| Restart policy | `unless-stopped` |
| Stop grace period | `120s` (`--stop-timeout=120` on Unraid). Keep it: a stop then never cuts a database update. |
| Log rotation | `json-file`, `max-size` `10m`, `max-file` `5` (Docker and Synology) |

## Ports

| Container port | Host port in the templates | Use |
|---|---|---|
| `8180` | `8180` | Web interface and API |
| `8182` | Not published | Optional token-gated diagnostics, off by default |
| `8443` | `AWESONIX_MAIL_DAV_HOST_PORT`, default `8443` (Docker and Synology) | Optional inbound CalDAV and CardDAV HTTPS listener |

Warning: Do not expose the diagnostics port or the DAV port directly to the public internet. First do a separate security review and set up a protected network boundary.

If diagnostics are disabled, keep port `8182` unpublished.

## Application settings

| Variable | Default | Meaning |
|---|---|---|
| `AWESONIX_MAIL_ENV` | `production` | `development`, `test`, or `production`. Keep `production` for every normal deployment. |
| `AWESONIX_MAIL_ATTACHMENT_LIMIT_MIB` | `20` | Maximum attachment size for each draft in MiB, from 1 to 100 |
| `AWESONIX_MAIL_SETUP_TOKEN_MINUTES` | `30` | Expiry of the first-run setup token in minutes, from 1 to 1440 |
| `AWESONIX_MAIL_STORAGE_WARNING_MIB` | `10240` | Free space in MiB for the warning level |
| `AWESONIX_MAIL_STORAGE_CRITICAL_MIB` | `5120` | Free space in MiB for the critical level |
| `AWESONIX_MAIL_STORAGE_RESERVE_MIB` | `1024` | Free space in MiB that Awesonix Mail keeps in reserve |
| `AWESONIX_MAIL_TRUSTED_PROXIES` | Empty: `127.0.0.0/8,::1/128,10.0.0.0/8,172.16.0.0/12,192.168.0.0/16,fc00::/7,169.254.0.0/16,fe80::/10` | Reverse proxies whose `X-Forwarded-For` entries count, as a comma-separated list of IP addresses or CIDR networks. The value `none` trusts no proxy. See [Reverse proxy](#reverse-proxy). |

The three storage values must be whole numbers in MiB. The warning value must be higher than the critical value. The critical value must be higher than the reserve value. The reserve value must be higher than 0.

### AI work

| Variable | Default | Meaning |
|---|---|---|
| `AWESONIX_MAIL_AI_DURABLE_ANALYSIS` | `true` | Keep it enabled for automatic analysis and re-analysis jobs. |
| `AWESONIX_MAIL_AI_DURABLE_BRIEFINGS` | `true` | Compatibility setting. Both values retain the chunk state that the briefing engine needs. |
| `AWESONIX_MAIL_AI_COMPLETION_NOTIFICATIONS` | `true` | Sends a notification when a Vigil briefing is ready. Each user controls Assistant alerts in the notification settings. |

### Diagnostics

| Variable | Default | Meaning |
|---|---|---|
| `AWESONIX_MAIL_DEBUG_LOG_ADDRESS` | `off` | Address of the diagnostics listener. The value `off` disables the listener. Set `0.0.0.0:8182` and publish port `8182` only for a diagnostic session on a trusted network. |
| `AWESONIX_MAIL_DEBUG_TOKEN_MINUTES` | `60` | Lifetime of the diagnostic bearer token in minutes, from 1 to 1440 |
| `AWESONIX_MAIL_LOCAL_DIAGNOSTICS` | `off` | Tokenless local diagnostics: `off`, `observe`, or `control`. Keep `off` in production. |
| `AWESONIX_MAIL_LOCAL_DIAGNOSTICS_OWNER` | Empty | Test-only owner email principal. Keep it empty if tokenless diagnostics are off. |

Use `0.0.0.0:8182` only with a protected diagnostics port. Awesonix Mail accepts `observe` and `control` only if `AWESONIX_MAIL_ENV` is `test`.

### Inbound DAV

| Variable | Default | Meaning |
|---|---|---|
| `AWESONIX_MAIL_DAV_ENABLED` | `false` | Enables the inbound CalDAV and CardDAV listener |
| `AWESONIX_MAIL_DAV_ADDRESS` | `0.0.0.0:8443` in the image | Listen address of the DAV listener |
| `AWESONIX_MAIL_DAV_BASE_URL` | Empty | Absolute HTTPS origin, without credentials, path, query, or fragment |
| `AWESONIX_MAIL_DAV_TLS_CERT_PATH` | Empty | Path to the TLS certificate |
| `AWESONIX_MAIL_DAV_TLS_KEY_PATH` | Empty | Path to the TLS private key |

Inbound DAV is disabled by default. If you enable it, set the HTTPS base URL, the certificate path, and the private key path. If one of these values is missing, DAV fails closed.

## First-run setup

Open `http://<host>:8180/setup`. The setup wizard has six steps, and you can resume it. It configures these items:

1. the database connection (read-only if the variables are set)
2. the sole founding administrator
3. the remote mail retention and the default cache quota
4. the default appearance
5. the licence review and acceptance
6. the final review

If you move from another installation, do these steps after the setup:

1. Sign in.
2. Go to **Settings > Account > Security**.
3. Import a settings bundle that is encrypted with a passphrase.

## Reverse proxy

Terminate TLS at a reverse proxy. Enable HTTP/2 and compression for text responses.

### Client address behind a proxy

Awesonix Mail uses the client address for sign-in throttling, audit records, the second-factor check, and the limits of public note links. It starts with the address of the direct peer. If that peer is a trusted proxy, it reads the `X-Forwarded-For` header from right to left. It uses the rightmost entry that is not a trusted proxy. If every entry is trusted, it uses the leftmost entry.

A malformed entry stops the walk at the last good address. For IPv6, the limits of public note links count each /64 network as one client.

`AWESONIX_MAIL_TRUSTED_PROXIES` sets the trusted proxies. The default trusts loopback, the private networks `10.0.0.0/8`, `172.16.0.0/12`, and `192.168.0.0/16`, IPv6 unique local addresses (`fc00::/7`), and link-local networks. This default covers a proxy on the same host, a proxy container on a Docker network, and the Docker Desktop gateway.

Make sure that the proxy appends the client address to `X-Forwarded-For` (in nginx, `proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;`). A proxy that passes the header through without its own entry lets a client choose its address.

With the default, a client in a private network can send its own `X-Forwarded-For` header and choose the address that Awesonix Mail counts. If you do not trust every device in your private networks, set the address or network of your proxy only. Examples are `172.18.0.0/16` and `192.168.1.10`. If clients connect directly without a proxy, set `none`.

Awesonix Mail already marks the content-hashed files in `/assets/` as immutable. It also makes `index.html` revalidate. As a result, browsers download a new build exactly once.

Use this configuration for nginx. It also works in the Advanced tab of Nginx Proxy Manager.

```nginx
gzip on;
gzip_vary on;
gzip_min_length 1024;
gzip_types text/css application/javascript text/javascript application/json image/svg+xml;
```

Without `gzip_types`, nginx compresses only `text/html`. The application bundle then travels about three times larger over the network.

## Notifications

In-app notifications need no external provider. Web Push is also self-hosted.

If you do not supply your own VAPID values, the supervisor does these things:

- It creates `/srv/secrets/vapid.private`.
- It reuses that key across restarts.
- It derives the public key.
- It enables browser delivery.

Warning: Preserve the secrets volume. If the VAPID key is replaced, browsers must create new subscriptions.

### Your own VAPID identity

To use your own VAPID identity, set all three variables together. Mount the private key inside the secrets boundary.

| Variable | Default | Meaning |
|---|---|---|
| `AWESONIX_MAIL_NOTIFICATION_VAPID_PUBLIC_KEY` | Empty | Uncompressed P-256 public key as a base64url value |
| `AWESONIX_MAIL_NOTIFICATION_VAPID_PRIVATE_KEY_PATH` | Empty | Path to the private key |
| `AWESONIX_MAIL_NOTIFICATION_VAPID_SUBJECT` | Empty | A `mailto` URI or an HTTPS URI |

### Phone notifications

The Android and iOS apps can fetch their notification feed directly from the paired server. Thus the self-hosted baseline does not require Firebase or APNs credentials.

The templates also contain optional variables for Firebase Cloud Messaging (FCM) and APNs. They are empty by default. Set each group together, or leave the whole group empty.

| Group | Variables |
|---|---|
| FCM development | `AWESONIX_MAIL_NOTIFICATION_FCM_DEVELOPMENT_PROJECT_ID`, `AWESONIX_MAIL_NOTIFICATION_FCM_DEVELOPMENT_CREDENTIALS_PATH` |
| FCM production | `AWESONIX_MAIL_NOTIFICATION_FCM_PRODUCTION_PROJECT_ID`, `AWESONIX_MAIL_NOTIFICATION_FCM_PRODUCTION_CREDENTIALS_PATH` |
| APNs development | `AWESONIX_MAIL_NOTIFICATION_APNS_DEVELOPMENT_TEAM_ID`, `AWESONIX_MAIL_NOTIFICATION_APNS_DEVELOPMENT_KEY_ID`, `AWESONIX_MAIL_NOTIFICATION_APNS_DEVELOPMENT_PRIVATE_KEY_PATH`, `AWESONIX_MAIL_NOTIFICATION_APNS_DEVELOPMENT_TOPIC` |
| APNs production | `AWESONIX_MAIL_NOTIFICATION_APNS_PRODUCTION_TEAM_ID`, `AWESONIX_MAIL_NOTIFICATION_APNS_PRODUCTION_KEY_ID`, `AWESONIX_MAIL_NOTIFICATION_APNS_PRODUCTION_PRIVATE_KEY_PATH`, `AWESONIX_MAIL_NOTIFICATION_APNS_PRODUCTION_TOPIC` |

Each user must explicitly allow notifications in the browser and on the phone.

## Health checks

The image health check runs the first-party `awesonix-mail healthcheck` command against `/health/ready`. The state is `starting` until the database build ends. If nobody finishes the first database step, the state turns unhealthy after the start period. A long first build on slow storage can take many minutes.

| Setting | Value |
|---|---|
| Interval | 30 seconds |
| Timeout | 5 seconds |
| Start period | 60 minutes |
| Retries | 3 |

## Upgrades

Warning: Before version 1.0, Awesonix Mail does not support a database downgrade. It also does not support compatibility with an embedded database.

Before an upgrade, do these steps:

1. Preserve the three application volumes.
2. Make a backup of the external database and verify it.
3. Before a breaking upgrade, export the portable settings.

Rehearse a restore separately.

Warning: Before you update to 0.108.0, back up the database. Migration 000190 drops the tables of the old briefing method. It has no data restore. Published briefings stay readable.

The settings `AWESONIX_MAIL_AI_DIAGNOSTICS_DIR` and `AWESONIX_MAIL_BRIEFING_LEGACY_KINDS` no longer exist. Remove them from your container or Compose file.

See the [operations guide](../operations/README.md) for coordinated backup, restore, monitoring, and clean-install recovery.
