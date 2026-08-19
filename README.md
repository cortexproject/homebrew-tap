# cortexproject/homebrew-tap

Homebrew formulae for [Cortex](https://cortexmetrics.io) tooling.

Currently just [`cortextool`](https://github.com/cortexproject/cortex-tools). Other
`cortex-tools` binaries — `benchtool` and friends — can join this tap later.

## Install

```bash
brew install cortexproject/tap/cortextool
```

The fully-qualified name above works as-is. To install by the short name instead, trust the
tap first — Homebrew 6 and newer will not load formulae from an untrusted third-party tap:

```bash
brew tap cortexproject/tap
brew trust cortexproject/tap
brew install cortextool
```

## What to expect

The formula **builds from source**, so Homebrew installs Go as a build dependency and the
install takes roughly a minute. There are no bottles.

Building from source is what lets one formula cover every platform Homebrew supports here:
macOS arm64, macOS x86_64, Linux arm64 and Linux x86_64. `cortex-tools` only publishes
`linux/amd64` binaries, so a binary-based formula could never serve Linux arm64.

## Why a tap and not homebrew-core

We submitted `cortextool` to homebrew-core as
[Homebrew/homebrew-core#299512](https://github.com/Homebrew/homebrew-core/pull/299512). The
formula itself was fine — it built, bottled, and passed `brew test` on Homebrew's CI — but the
submission was declined on
[notability](https://docs.brew.sh/Acceptable-Formulae#niche-or-self-submitted-stuff):
`cortex-tools` does not yet have enough stars, forks and watchers, and a maintainer told us to
"distribute this software via your own tap instead." So here we are. If `cortex-tools` grows
past those thresholds, homebrew-core is still the better home.

## How this tap stays up to date

The `Update cortextool` workflow bumps the formula on its own. It looks for a newer
`cortex-tools` release every 6 hours, and `cortex-tools`' release workflow also sends a
`repository_dispatch` so a new release is usually picked up within a minute.

When it finds one it rewrites `url` and `sha256` with `brew bump-formula-pr --write-only`, then
builds and `brew test`s the candidate on macOS arm64, macOS x86_64, Linux x86_64 and Linux
arm64. It commits to `main` **only if every platform passes**.

To bump by hand — after a retag, or to skip the wait:

```bash
gh workflow run "Update cortextool" --repo cortexproject/homebrew-tap
gh workflow run "Update cortextool" --repo cortexproject/homebrew-tap -f tag=v0.21.1
```

Pull requests get the same checks, minus the bump: `brew style`, `brew audit --strict`, and the
same four-platform install and `brew test`.
