# Third-party source availability

Exact component families, revisions, licence expressions, file hashes, and preserved notices are recorded under `third-party/`. The runtime image contains no GPL- or LGPL-covered executable or library component.

The MPL-2.0-covered MySQL driver is used unmodified at `github.com/go-sql-driver/mysql` revision `v1.10.0`. Its exact Go module source archive is included under `third-party/go/sources/`; the manifest records both its archive SHA-256 and Go module checksum.

The runtime trust store is copied from Alpine package `ca-certificates 20260909-r0` and derived solely from MPL-covered Mozilla `certdata.txt`. Its exact upstream source archive, Alpine build recipe, MPL text, hashes, and derivation are included under `third-party/runtime-data/`. Time-zone data is copied from Alpine package `tzdata 2026d-r0`; its exact IANA source archive, public-domain notice, Alpine build recipe, and hashes are included alongside it. Build-only container stages are not shipped in the release image.

The source archives travel with the release so compliance does not depend on an upstream URL remaining available. Contact support@awesonix.com with the Awesonix Mail version and build number if any evidence appears incomplete.
