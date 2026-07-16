# cliphist vendor archive

`cliphist-0.7.0-vendor.tar.xz` contains only the `vendor/` directory generated
from the official `sentriz/cliphist` v0.7.0 source. It replaces the previous
dependency on the third-party `henri-gasc/cliphist` fork, which copied the
official source and checked a generated vendor tree into its release archive.

The overlay updates `golang.org/x/image` from v0.21.0 to v0.44.0 to fix the
reachable TIFF decoder vulnerabilities in the released dependency set. That
module requires Go 1.25, so the generated lock changes the main module's Go
directive from 1.20 to 1.25.0. The corresponding `go.mod`/`go.sum` patch is in
the parent `nekochigura` repository.

- Upstream tag: `v0.7.0`
- Upstream commit: `efb61cb5b5a28d896c05a24ac83b9c39c96575f2`
- Source URL: `https://github.com/sentriz/cliphist/archive/refs/tags/v0.7.0.tar.gz`
- Source SHA256: `8d7dc7b4495e5812b5613274c250ba5d3900933d78888ce7921c01247f191cc8`
- Source date: `2025-10-11T13:22:56Z` (`SOURCE_DATE_EPOCH=1760188976`)

The published archive was generated with Go 1.26.4, GNU tar 1.35, and XZ
Utils 5.8.3. Starting in a temporary directory containing the verified
upstream archive:

```sh
export LC_ALL=C TZ=UTC SOURCE_DATE_EPOCH=1760188976
export GOTOOLCHAIN=local
umask 022

base=$PWD
work=$base/work
source=$base/cliphist-0.7.0.tar.gz
output=$base/cliphist-0.7.0-vendor.tar.xz
printf '%s  %s\n' \
  8d7dc7b4495e5812b5613274c250ba5d3900933d78888ce7921c01247f191cc8 \
  "$source" | sha256sum --check --strict -
mkdir -p "$work"
tar -xzf "$source" -C "$work"
cd "$work/cliphist-0.7.0"
go mod edit -go=1.25.0 -require=golang.org/x/image@v0.44.0
GOCACHE="$work/go-build" \
GOMODCACHE="$work/go-mod" \
GOPROXY=https://proxy.golang.org \
GOSUMDB=sum.golang.org \
go mod tidy
GOCACHE="$work/go-build" \
GOMODCACHE="$work/go-mod" \
GOPROXY=off \
go mod vendor
find vendor -type d -exec chmod 0755 {} +
find vendor -type f -exec chmod 0644 {} +
tar --create \
  --format=gnu \
  --sort=name \
  --mtime="@${SOURCE_DATE_EPOCH}" \
  --owner=0 --group=0 --numeric-owner \
  --mode='u+rwX,go+rX,go-w' \
  --directory="$work/cliphist-0.7.0" vendor \
  --file="$base/cliphist-0.7.0-vendor.tar"
xz --threads=1 --check=crc64 -9e "$base/cliphist-0.7.0-vendor.tar"
sha256sum "$output"
```

Expected archive size: `692916` bytes

Expected SHA256:

```
045de9f3c291fb2bf190af9724cec8689310ac577f599d4890a2bc381cd214cf  cliphist-0.7.0-vendor.tar.xz
```
