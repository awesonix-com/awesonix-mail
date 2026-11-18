# Third-party compliance bundle

This governed bundle records every third-party component shipped by Awesonix Mail. It is generated from the linked Go dependency graph, the production npm lockfile closure, pinned webfont assets, and the two data packages copied into the final runtime image.

| Area | Manifest | Evidence |
|---|---|---|
| Go binaries | `go/manifest.json` | Exact linked module versions, licence and notice files, and MPL source archives |
| Web bundle | `web/manifest.json` | Exact production package versions, integrity values, repositories, and licence files |
| Fonts | `fonts/manifest.json` | Exact CDN revisions, asset hashes, OFL texts, and font authorship records |
| Runtime data | `runtime-data/manifest.json` | Exact Alpine package revisions, upstream sources, build recipes, licences, and hashes |

The Awesonix proprietary agreement applies only to Awesonix-owned material. It does not restrict rights granted by the files in this directory.

Regenerate this bundle whenever dependencies, fonts, runtime-data revisions, or relevant build inputs change. Release packaging and container builds reject missing, unexpected, or stale governed evidence.
