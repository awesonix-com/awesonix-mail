# Docker Installation

Provision an independently managed database server and a dedicated user first (tested with MariaDB 11.4). The database variables are optional. Leave `AWESONIX_MAIL_DATABASE_HOST` and `AWESONIX_MAIL_DATABASE_PASSWORD` empty to enter the database settings in the first-run wizard. Or export `AWESONIX_MAIL_DATABASE_HOST`, `AWESONIX_MAIL_DATABASE_PORT`, `AWESONIX_MAIL_DATABASE_NAME`, `AWESONIX_MAIL_DATABASE_USER`, and `AWESONIX_MAIL_DATABASE_PASSWORD`. The variables always win.

Verify and load the image from the [GitHub Release page](https://github.com/awesonix-com/awesonix-mail/releases/tag/v0.115.4) and run:

```sh
docker compose config
docker compose up -d --no-build
docker compose ps
```

The Compose project tracks `ghcr.io/awesonix-com/awesonix-mail:latest`. Open `http://<docker-host>:8180/setup` for first-run product setup. It uses named volumes `awesonix-mail`, `awesonix-secrets`, and `awesonix-logs`; database files and database backup/restore remain the database operator's responsibility. The wizard saves its database settings as `database.json` in `awesonix-secrets`: back it up with `master.key`. The template sets a stop grace period of 120 seconds; keep it. If the database step waits or fails, see the troubleshooting guide in the `docs` folder of this package.

Upgrade a registry installation with `docker compose pull` followed by `docker compose up -d`. For offline installation, verify and load the versioned archive, then run `docker tag awesonix-mail:0.115.4 ghcr.io/awesonix-com/awesonix-mail:latest` before creating the container. Never use `docker compose down -v` for an installation whose data must be retained.
