# Version 0.1.8 audit

Audit date: 2026-11-02 UTC. The audited test build is published as the
[0.1.8 GitHub prerelease](https://github.com/calcitequicksolve/CalciteQuickSolve/releases/tag/v0.1.8).
Production automatic updates remain unchanged; this build is in the beta channel.

## Scope

The audit covers the existing calculator and note features, the 0.1.8 history and
synchronization requests, desktop and Android presentation, and GitHub update
hosting. The hosted server remains a specification for separate implementation.
Production note folders, the running user's app and production Syncthing settings
are not used as test fixtures.

| Area | Verification |
| --- | --- |
| Calculator, sequences, variables/functions, programmer integers, number display | Unit tests and full/quick browser workflows. |
| 2D/3D graphs, resizing, gestures, saved views and previews | Unit, desktop browser and simulated Android layout/gesture workflows. |
| Notes, tags, labels, search, archive/Trash, imports, attachments and exports | Unit, browser and native storage tests, including exact attachment bytes and recovery chunks. |
| Tab/Shift+Tab, focus escape and browser undo/redo | Browser keyboard checks, with the default and disabled preference. |
| Workspace selection, moves/copies, QR identity, missing folders and reinitialisation | Native and browser checks; Android provider/private-folder tests. |
| Vault, keys, recipients, encryption/signatures, drafts and backups | Native cryptographic/storage regressions and browser workflows, including retrieval after trial expiry. |
| Basic/Advanced history, restore, rewrites, manual and automatic policies | Pure history tests, native storage integration and browser review workflows. |
| Active typing, caret, backward selection, scroll and asynchronous updates | Seeded text/cursor tests, browser workflows and real native Syncthing scenarios. |
| Semantic search | Unit/UI controls plus the pinned real model's offline import and semantic ranking tests. |
| MCP and licensing | Native authorization/tool tests, browser configuration, real Bash/Node/PHP signature interoperability and the PHP purchase-service fixture. |
| Updates and packages | Policy/UI tests, pinned GitHub URL tests, real native GitHub discovery, installer signature verification and exact package/upload checks. |

## Fixed during the audit

- Immediate reload could recover an older shared pending draft instead of the
  latest synchronous window journal. Recovery now combines those copies from the
  recorded base. A regression holds shared writes pending through reload.
- A pending note in another window could remain marked unsaved after its first
  save elsewhere. Refresh now checks actual saved storage rather than treating a
  remaining shared draft as proof that no save exists.
- Closing Settings during an active save could produce a false unsaved-settings
  warning. Navigation waits for that save's persistence result.
- A background note refresh could hide a rejected attachment's error. File errors
  now remain visible until the next file attempt for that note.
- Several compatible common ancestors from different four-way merge orders were
  treated as an unresolved conflict. The engine now recursively combines verified
  compatible bases before merging later edits. Genuine overlaps, incompatible
  bases and bounded-work failures still require review. The comparison discloses
  when Base combines several earlier versions.
- Review-every-change could strand a reviewed resolution on an older head when
  another client joined its history without changing its content. Verified
  descendants with identical authored content and protection now advance without
  another prompt. Genuine new edits and new notes still require review.

Older tests were updated for the current toolbar, compact mobile controls,
workspace-add flow, history API and first-save behavior. Native-save probes now
verify the requested text in native storage, rather than treating a transient
Saved label from a previous save as an acknowledgement.

## Completed checks

- Svelte: zero errors and zero warnings.
- Frontend/signing units: **453 passed** across 35 files; after splitting the
  repositories, **445** run in Core and **8** in Website/signing.
- General Trial browser build: **275 passed**, 10 cases reserved for other build
  configurations.
- Free licensing-fixture browser build: **41 passed**, two edition cases skipped.
- Full browser build: **1 passed**, two other edition cases skipped. Together the
  configurations exercise all **285** declared app browser cases, with **317**
  successful executions across overlapping suites.
- Native app: **171 passed**; all **3** optional tests passed separately against
  the final build (real semantic model import/ranking and installer signature/version).
  The suite runs with `--test-threads=1` because vault fixtures share process state.
- Pure history core: **10 passed**; its optional cross-language vector replay
  passed separately. QR core: **14 passed**.
- Android Kotlin: **34 passed**, including folder providers, sync planning,
  private-folder deletion, picker lifecycle and device-vault operations.
- Text/cursor fuzzing: five seeds × 25,000 iterations, **1,000,000 properties**;
  **219,652** exhaustive repeated-text edit pairs and **40,454** caret cases.
- Native parity: **375,000** generated text-merge vectors and **219,652** exhaustive
  vectors replayed in both orders.
- History-graph fuzzing: **10,000** four-writer histories, **1,000,000** scheduled
  actions, and **52,574** compatible recursively combined bases. Every acknowledged
  save remains in final ancestry, and all four final states converge.
- Actual QR Transceiver core interoperability: three frame sizes, multiple files,
  static transfers, binary return, password retry and encrypted return.
- License signing interoperability and **68** PHP purchase/policy/rejection
  checks; eight simultaneous fixture requests issue one identical license.
- Website: zero Svelte errors/warnings, **15** unit tests, production build and
  **24** browser tests passed with the imported GitHub release metadata. The
  Android download and checksum links point to the published GitHub assets.

## Real Syncthing result

Four separate native Windows CQS instances used the installed Syncthing 2.1.7
through a fifth passive relay. All **16 scenarios passed** in the final run,
2026-11-02 12:01–12:22 UTC. They cover concurrent typing/saves, four offline
branches, backward selections, scrolled carets, manual resolution, automatic
mine/theirs, attachments, missing parents, closed-app overwrites, older
projections with conflict copies disabled, recovery of missing shared history,
relay restart, deletion/edit conflicts, review-every-change across restart, and
encryption concurrent with an offline edit while another client remains locked.

The driver checked **513** caret positions and observed remote updates during
continuous typing. No page errors were recorded. The independent ledger audit
verified **1,000 history objects** and checked every one of the **80 acknowledged
saves on all four clients** (320 ancestry/content checks). All five Syncthing
peers ended idle with zero pending items and zero pull errors. The encryption
case retained the offline edit, preserved the chosen encryption and cleared the
conflict on the locked client after unlock without another resolution.

Receipts are under `artifacts/syncthing-native-0.1.8/2026-11-02T12-01-21-450Z/`
and `artifacts/syncthing-native-0.1.8/ledger-audit.json` in the development
workspace. Acknowledgement checks read the actual saved native content, not an
earlier Saved label.

## Rebuilt packages and publication

| Package | SHA-256 |
| --- | --- |
| Windows installer | `4faf5a34f2c5ec66213f7d618af75a1bda11c1bd33d34560e6e5b777b7833334` |
| Windows standalone | `233e04967388071544e4017473dc61d2eae946e4c3185a98a15abd97eb888265` |
| Android ARM64 preview | `50ab45ca3da73aeaa740ff962397afd116bd9c3e0bc38d444412070a02bc2fbc` |

The installer updater signature verifies, including rejection of modified bytes.
Windows binaries remain Authenticode-unsigned. The APK contains the newly built
ARM64 library and the same frontend embedded in Windows. ZIP/native load
alignment is 16 KB. Its identity is `com.calcitequicksolve.android.debug`, version
0.1.8 / code 1008, minimum Android 11 / target SDK 36, using the existing preview
certificate.

All **11** GitHub Release assets were downloaded and matched byte for byte before
the beta feed was pushed. A real native test instance then verified GitHub
production/beta discovery, exact installer/EXE hashes, the master disable switch
and standalone download/install boundaries. No production updater installation
was performed. The public commit and release tag have verified CalciteQuickSolve
SSH signatures. Package receipts are in `artifacts/github-0.1.8/`.

The APK was installed with `adb install -r` on the SM-F976W without resetting app
data. Its installed base APK was pulled and matched the verified SHA-256 above;
the updated app launched and remained running.

## Practical limits

Browser Android checks exercise the frontend with native-command fixtures;
Kotlin tests exercise provider operations independently. The final phone install
and launch are verified, but its personal notes were not used as test fixtures.
Actual Google Drive/OneDrive clients, camera scanning, biometric hardware and Play
distribution were not exercised. The Syncthing scenarios used real installed
transport and separate native Windows profiles with synthetic notes. No
production purchase or production-profile updater installation was performed.

These checks provide reproducible evidence for the tested cases, rather than a
guarantee against every possible provider, hardware or disk failure. Keep complete
independent backups and retain provider conflict copies.
