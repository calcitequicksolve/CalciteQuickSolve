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

The Android packaging correction was checked separately: the installed 0.1.8
APK and corrected 1.0.0 APK have the same application ID and signing certificate.
Release guards accepted the corrected APK and rejected the incompatible signed
APK, the debuggable preview, and the incompatible publication manifest. All 17
website unit tests, Svelte checks, the static build, three release-page browser
cases and the offline Help browser case passed again. Bridge command retention
and 16 KB alignment passed on the corrected APK. The earlier merge/native audit
was not rerun for this signing, packaging and documentation correction.

## Package identities

| Package | SHA-256 |
| --- | --- |
| Windows installer | `70ec1dede08feb863cac6c5dcd51ac53daeda3a01c0d008d122099be5892a254` |
| Windows standalone | `bfef266ee33de4b0389183090442a07c2c0aed88e159e02cbae23491abf39977` |
| Android release APK | `905b1972623be07ae84d44bec6887dcabf0d1574db3415f2ff5569596292b396` |

Windows binaries are Authenticode unsigned. The installer has the verified CQS
updater signature. Its native code contains the GitHub feed URLs and no old
website update-feed URLs. Windows binaries retain the original 1.0.0 frontend. The corrected APK embeds
the same application behavior with Android offline Help updated to describe
in-place upgrades; its frontend identity is recorded separately.

The APK is release-signed with the original installed certificate, application
ID `com.calcitequicksolve.android.debug`, version name `1.0.0`, version code `1000000`,
minimum API 30, target API 36 and `debuggable=false`. Its certificate SHA-256 is
`a1d5809308571099936732a84676143018c03cc2b9defb6ed9c841a1d51cacbc`.
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

The corrected Android APK updates the earlier APK in place. The historical
`.debug` application-ID suffix is permanent and does not make release builds
debuggable. The initial 1.0.0 APK used an incorrect ID and certificate; its bytes
remain archived locally and its public asset has been replaced. See the
[Android guide](android.md).

The corrected APK was installed over 0.1.8 on the phone and launched successfully.
Its certificate matched the installed APK; Android retained the application UID,
original installation time, data path and both credential/device storage inodes.
No uninstall or data clear was performed. Personal notes were not read or used
as fixtures, and the user's Windows profile was not modified. Physical
camera/biometric workflows and third-party Drive/OneDrive clients were not newly
tested. Keep independent backups and retain sync-provider conflict copies.
