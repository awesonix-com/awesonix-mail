# Synology DSM Installation

This package targets DSM 7.2 Container Manager on a Linux AMD64 Synology NAS.

1. Let Container Manager pull `ghcr.io/awesonix-com/awesonix-mail:latest`. For an offline installation, verify and load `awesonix-mail-0.115.2-linux-amd64.tar` (download it from the [GitHub Release page](https://github.com/awesonix-com/awesonix-mail/releases/tag/v0.115.2)), then over SSH run `docker tag awesonix-mail:0.115.2 ghcr.io/awesonix-com/awesonix-mail:latest` before starting the project.
2. Provision an independently managed database server and a dedicated user (tested with MariaDB 11.4). The database variables are optional. To use them, define the server address, port, database name, username, and password in the Container Manager project environment. If you leave the address and password empty, the first-run wizard asks for them.
3. Create a shared folder such as `/volume1/docker/awesonix-mail` and copy `docker-compose.yml` into it.
4. In **Container Manager > Project**, choose **Create**. Use project name `awesonix-mail`, select that folder as the path, and upload or select `docker-compose.yml` as the source.
5. Start the project, then open `http://<nas-address>:8180/setup`.

The Compose project stores Awesonix data in `./data/mail`, `./data/secrets`, and `./data/logs` beneath the project path. Preserve them when updating. If the wizard saved the database settings, `./data/secrets/database.json` holds them: back it up with `master.key`. The template sets a stop grace period of 120 seconds; keep it. Database files, lifecycle, backup, restore, and availability remain the NAS operator's responsibility.
