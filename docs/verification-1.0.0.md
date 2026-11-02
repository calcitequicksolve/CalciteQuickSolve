# 1.0.0 release verification

Verified on November 2, 2026. Version 1.0.0 is the production release for Windows
x64 and Android 11+ ARM64. Earlier binaries have been withdrawn from public
download; their exact bytes and the previous website files remain archived locally.

## Changes and regression coverage

This release changes the version and release date, refreshes the shared guides,
publishes GitHub production update metadata and builds a signed Android release
package. The merge, history, cursor, workspace and native updater implementations
match the audited pre-release source. The [synchronization audit](verification-0.1.8.md)
records the four-client Syncthing scenarios, acknowledged-save checks, cursor
testing and text/history fuzzing; those expensive scenarios were not repeated for
the version and documentation change.

- Frontend: Svelte check passed with no errors or warnings; all 445 unit tests passed.
- Native: all 171 regular tests passed serially. The new installer's signature and
  embedded version passed verification; modified installer bytes were rejected.
- Offline Help: the browser regression passed with external requests blocked,
  including the updated Android guide, links, screenshots and narrow layout.
- Website: 17 unit tests, no Svelte errors or warnings, a production static build,
  and all 24 browser cases passed. One assertion still expected a website-hosted
  EXE; it was corrected to require the GitHub URL and rerun successfully.
- The website download catalog contains only 1.0.0. Its build contains metadata,
  checksums, guides and assets, with no EXE or APK files.

## Package identities

| Package | SHA-256 |
| --- | --- |
| Windows installer | `70ec1dede08feb863cac6c5dcd51ac53daeda3a01c0d008d122099be5892a254` |
| Windows standalone | `bfef266ee33de4b0389183090442a07c2c0aed88e159e02cbae23491abf39977` |
| Android release APK | `b46c28675ebcba6f04ff323c22b9da5dffc22d3c64efdeb0b71a65bb4d4b0a32` |

Windows binaries are Authenticode unsigned. The installer has the verified CQS
updater signature. Its native code contains the GitHub feed URLs and no old
website update-feed URLs. The APK and Windows binaries embed the same production
frontend, including the updated offline Help.

The APK is release-signed with the existing production certificate, application
ID `com.calcitequicksolve.android`, version name `1.0.0`, version code `1000000`,
minimum API 30, target API 36 and `debuggable=false`. Its certificate SHA-256 is
`a352c49631d24b4ac6513c5748cbfaab8c9ba957e9db8cfd0a01af89348df47a`.
Native libraries and ZIP offsets satisfy 16 KB alignment. Package inspection
verified the newly built ARM64 library, all annotated bridge commands, workspace
recovery guards and Android/desktop history-command parity after optimization.

## Distribution and limits

All 11 GitHub Release assets were downloaded and matched byte for byte before
the production feeds were pushed. Public downloads subsequently returned success
with the expected sizes. An isolated native 0.1.8 installation discovered 1.0.0
through GitHub with matching installer/EXE hashes, both with production preferences
and beta opt-in. The disable switch and standalone installation restrictions also
passed; no user installation was modified.

After deployment, all 184 publicly readable website files matched the built bytes.
All 15 previous website binary URLs returned HTTP 410. The old 0.1.8 GitHub Release
was removed after its local archive was verified; 1.0.0 is the only public release.
The six GitHub feed/platform metadata files match the staged production values,
with no active beta. Commits and the 1.0.0 tag use verified CalciteQuickSolve SSH
signatures. Publication receipts are retained in `artifacts/github-1.0.0/` locally.

Windows production update discovery and signed downloads use the public GitHub
repository. Beta updates remain opt-in. Android users download and install the
signed APK; there is no automatic Android updater or Play Store publication.

The production Android app installs separately from the retired `.debug` preview.
Transfer complete workspaces and matching keys and verify the result before
removing that preview. See the [Android guide](android.md).

No personal notes were used as fixtures. This release check did not install over
the user's Windows profile or install the production APK on the phone. Physical
camera/biometric workflows and third-party Drive/OneDrive clients were not newly
tested. Keep independent backups and retain sync-provider conflict copies.
