# Artifacts

The offline image for Awesonix Mail 0.115.4 is attached to the [GitHub Release page](https://github.com/awesonix-com/awesonix-mail/releases/tag/v0.115.4) and is not stored in this directory. Download these two files into one folder:

Source state: clean source revision; eligible for publication after all release gates pass.

```text
awesonix-mail-0.115.4-linux-amd64.tar
SHA256SUMS
```

Verify the checksum before loading the image.
The root `distribution-manifest.json` also records the build number, source revision, source state, image digest, archive size, and checksum.

POSIX:

```sh
sha256sum -c SHA256SUMS
docker load -i awesonix-mail-0.115.4-linux-amd64.tar
```

PowerShell:

```powershell
$expected = (Get-Content SHA256SUMS).Split(' ')[0]
$actual = (Get-FileHash awesonix-mail-0.115.4-linux-amd64.tar -Algorithm SHA256).Hash.ToLowerInvariant()
if ($actual -ne $expected) { throw 'Awesonix Mail artifact checksum mismatch' }
docker load -i awesonix-mail-0.115.4-linux-amd64.tar
```

The archive loads `awesonix-mail:0.115.4`. Linux AMD64 is the qualified offline platform for this package. ARM64 image construction is verified separately, but no ARM64 offline archive is shipped until native runtime qualification is complete.
