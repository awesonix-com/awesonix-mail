# TrueNAS Custom App preparation

This template is prepared for the Custom App YAML editor. The customer has
deferred host testing; **no TrueNAS release or CPU architecture has been
qualified for Awesonix Mail.** The release
archive attached to the [GitHub Release page](https://github.com/awesonix-com/awesonix-mail/releases/tag/v0.115.4) is for Linux AMD64; confirm the image and
architecture available for the target before installation.
The packaged offline archive is `awesonix-mail-0.115.4-linux-amd64.tar` (download it from the [GitHub Release page](https://github.com/awesonix-com/awesonix-mail/releases/tag/v0.115.4)).

1. Provision a separate database server (tested with MariaDB 11.4) and a
   dedicated user. Record its reachable host, port, database name, username and
   password. Do not use `127.0.0.1` for a database in another container.
2. Create three distinct persistent host dataset directories for mail, secrets
   and logs before deployment. Check permissions for the application and back up these directories
   and the separate database. The secrets directory contains the master key and,
   if the first-run wizard saved the database settings, `database.json`. Protect
   it and back both up. Do not use the placeholder paths as actual datasets.
   The image initialises its storage for UID/GID 10001; do not override its user
   or bootstrap. Long bind syntax prevents Compose from creating a missing host
   path; see [Docker bind options](https://docs.docker.com/reference/compose-file/services/#long-syntax-5).
3. Edit `docker-compose.yml` in a local editor. Replace every `REPLACE_` value
   with the three absolute dataset paths. The database host and password are
   empty: the first-run wizard asks for them. To set them in the YAML instead,
   fill them in; then the variables win. Adjust the database port, name and user
   as needed. Encode values as valid YAML:
   for a single-quoted value, double any single quote inside it. For Compose,
   double each literal dollar sign as `$$`, even inside YAML quotes.

   For example,
   the synthetic password `pa$/:'#` is written `'pa$$/:''#'`. See
   [Compose interpolation](https://docs.docker.com/reference/compose-file/interpolation/).
   Change the host side of port `8180:8180` if needed.

   DAV is disabled and port 8443 is unpublished. To enable DAV, uncomment its
   port mapping and configure its base URL
   and certificate/key paths and mount protected files at those paths. Review
   trusted proxies and optional application settings for the deployment.
4. In TrueNAS, open **Apps → Discover → more menu → Install via YAML**, give the
   app a valid name, and paste the edited Compose YAML into Custom Config. Save
   starts deployment. Follow the [TrueNAS installation guide](https://apps.truenas.com/managing-apps/installing-custom-apps/).
   This workflow does not assume an adjacent `.env` file. If you set the
   database password in the YAML, the pasted configuration contains it; protect
   access to it.
5. Once running, open `http://<truenas-host>:8180/setup`, using the selected
   host port. The image keeps its own healthcheck; diagnostics are off and not
   published by this template. The template sets a stop grace period of 120
   seconds; keep it. If the database step waits or fails, see the troubleshooting
   guide in the `docs` folder of this package.

For upgrades or recreation, preserve all three datasets and the external
database data and backups. Review the image identity and configuration before
replacing the app; never delete those datasets as a routine upgrade. Validate
installation, permissions, setup, login, synchronisation, recreation and
upgrade on the exact TrueNAS release and CPU architecture before claiming
support. A Compose configuration check alone does not qualify TrueNAS.
