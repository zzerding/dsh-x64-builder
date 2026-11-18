# dsh-x64-builder

Unsigned macOS **Intel (x64)** builds of [DeepSeek Harness Desktop](https://github.com/deepseek-ai/deepseek-harness).

Upstream publishes an Intel installer at
`https://download.deepseek.com/desktop/dsh-latest-macos-x64.dmg`, but that fixed
object trails the Apple Silicon build by weeks (checked 2026-11-17: x64 last
modified 2026-10-17, arm64 and Windows 2026-11-13). This repository rebuilds the
newest upstream `dsh-v*` tag on a GitHub-hosted `macos-15-intel` runner and
attaches the DMG to a release, so an Intel Mac can run the current version.

## How it runs

`.github/workflows/build-macos-x64.yml` checks every 6 hours, resolves the newest
`dsh-v*` tag, and skips versions that already have a release. A build takes about
35 minutes; the release tag is `x64-<version>`.

Watch this repository (Custom → Releases) to get a GitHub notification when a new
build lands.

## Using a build

```sh
gh release download x64-<version> --repo zzerding/dsh-x64-builder
open deepseek-harness-<version>-mac-x64-unsigned.dmg   # drag into Applications
xattr -dr com.apple.quarantine "/Applications/DeepSeek Harness.app"
```

## What these builds are not

- Not signed with a Developer ID and not notarized: the bundle is ad-hoc signed,
  so Gatekeeper blocks the first launch until the quarantine attribute is cleared.
- No Computer Use helper: staging it requires Developer ID and notary credentials.
- The packaging command validates the app with a runtime smoke test after building
  the DMG. That smoke test can exceed LibreOffice's internal conversion timeout on
  a cold runner, so a workflow run may show a failed `Build unsigned installers`
  step while the release asset is still produced and valid.