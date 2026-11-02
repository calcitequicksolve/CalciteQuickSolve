# CalciteQuickSolve

A scientific calculator with graphs, reusable variables and functions, and Notes
with local storage, encryption, immutable version history and folder synchronization.

[Download Windows and Android builds](https://github.com/calcitequicksolve/CalciteQuickSolve/releases).
Release assets include checksums. The Windows installer and standalone EXE use
WebView2. Android packages labelled **preview** are Android 11+ ARM64 test builds.

## Documentation

The `docs` directory contains the user guides bundled with the application.
Start with [Getting started](docs/getting-started.md), [Notes](docs/notes.md),
[workspaces and synchronization](docs/sync.md), or the [Syncthing example](docs/syncthing.md).
Guides are also available offline in Settings → Help & Docs.

Compatible changes merge automatically by default. Genuine conflicts preserve
both histories and can be resolved in CQS once for all syncing installations.
Keep sync-provider conflict copies enabled and keep independent backups.

## Updates

Application update metadata is hosted in `updates/` in this repository. Binaries
are versioned GitHub Release assets. Windows installed copies verify updater
signatures before installing. Standalone copies open GitHub Releases for manual
replacement. Android previews are updated using the APK installer.

The `main` branch's channel metadata is updated only after a release's files and
checksums are ready. An empty channel means no release has been published there.
This repository contains public documentation and release metadata; the source
repositories are private.
