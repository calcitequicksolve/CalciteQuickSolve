# Use CalciteQuickSolve on Android.

Find your way around the phone UI and understand which desktop workflows are still unavailable.

<a id="status"></a>

## Install the Android app

Download the signed 1.0.0 ARM64 APK from the official CalciteQuickSolve GitHub Release. It requires Android 11 or later. Allow installation from your browser or file manager when Android asks, then open the downloaded APK. This is a direct APK distribution; it does not imply a Play Store listing.

- The production app uses com.calcitequicksolve.android and installs separately from the old .debug preview. Keep the preview installed until your notes, attachments and keys are verified in the production app. Transfer complete workspaces using QR, shared folders or supported exports, and unlock or import the matching keys. Private app storage and device-bound vault credentials do not migrate automatically.
- Calculator behavior, note formats, trial/licensing rules and portable encryption formats are shared with desktop.
- Full-profile backup restore, ordered document tabs and file/attachment share intents remain unfinished. Shared workspace folders are available through the Android folder picker; main-library switching and automatic plain/encrypted exports remain Windows features.
- Desktop tray, global shortcuts, Quick windows and popped-out windows are not Android features.
- Future APK updates must use the same application ID and signing certificate. Install them over the current app to retain its data; do not uninstall first. Download links and checksums are on GitHub Releases.

<a id="navigation"></a>

## Navigate on a phone

Use Calc, Notes, Search and the Settings gear in the top row. Settings uses a category selector. The refresh icon in History forces a scan and reload after a folder sync. History and modal actions such as Edit and Close use a shorter mobile layout.

- Back from an open note uses its save/discard guard. Back from Settings keeps unsaved changes until you choose to save or discard them. Saving settings stays on the page.
- New notes can choose their workspace before their first save in the Workspace accordion. Saved notes use Move to workspace or Copy to workspace.
- Live Markdown/text and live encrypted disk exports are hidden on Android. Normal supported note exports, attachments, QR and shared workspace history remain available.
- MCP, desktop hotkeys, tray controls and the Windows updater are desktop features. Android updates are installed from the signed APK on GitHub Releases.

<a id="graphs"></a>

## Explore graphs with touch

Use plot(...) for a 2D graph or plot3d(...) for a surface. On Android, pinch with two fingers to zoom; drag with one finger to pan a 2D graph or move a 3D view.

- Open Graph ranges to enter explicit bounds, then choose Apply ranges. Use Reset to restore the view.
- Graph interactions keep the calculator keyboard closed. Tap Expression or the Calc tab when you want to type again.
- Scientific and programmer buttons adapt to the available width. Hold a button to read its meaning without inserting it.

<a id="notes"></a>

## Create notes and transfer by QR

Start the Notes trial or activate a license in Settings → License. In an empty Notes view, use the create, file-import or QR-import icons; hold an icon to read its explanation.

- Use Settings → Files & sync to create a shared folder workspace, a private local workspace, or receive one by QR. Shared folders can be selected in Syncthing; private local and QR-received workspaces remain inside the app until you move or copy notes into a shared workspace.
- Set up and unlock the receiving local vault first. Begin requests camera access. Keep the code in focus; use Scanner options → Compatibility if needed. Auto-lock pauses through transfer and review.
- Red indicates searching, amber indicates a detected QR, and green indicates a valid transfer frame. Wait for the review, then Import and merge before stopping the sender.

[QR transfers: sending, receiving and troubleshooting](qr-transfers.md)
[Notes, metadata and recovery](notes.md)

<a id="privacy"></a>

## Screenshots and vault unlock

Android blocks screenshots by default. Settings → General → Allow screenshots and screen recording is a device-local opt-in; captured content can include any unlocked note or sensitive detail visible on screen.

- The app reapplies its protected-window flag on startup and when it leaves the foreground. Verify behavior on your device before relying on recording or task-preview protection.
- Vault unlock can use configured strong biometrics or the phone’s screen-lock PIN/password. There is no separate app PIN. Master-password and recovery routes remain available for existing vaults.
- For protected workspace sync, unlock your local vault and import the workspace key through its workspace password or an encrypted QR transfer. Android biometrics unlock only keys held on this phone. See Security & backups for switching to or from a master password.

[Security and backups](security-and-backups.md)

<a id="help"></a>

## Read these guides offline

Open Settings → Help & Docs. The guide text and included screenshots are bundled in both the Windows build and Android package, so reading them does not need a network connection.

> Desktop screenshots illustrate the shared workflows; Android uses the phone navigation described here. External website links still require a connection.

<a id="shared-folders"></a>

## Use a shared folder

Android keeps the original General workspace private. Create a separate shared workspace when you want another app to synchronize its notes. Calcite uses Android’s folder permission; it does not require root or all-files access.

1. Open Settings → Files & sync → Workspaces, enter a name, and leave Storage set to Shared folder. Choose Create empty workspace.
2. In Android’s folder picker choose a parent under Documents and allow access. Calcite creates a Calcite-Workspace-<name>-<UUID> subfolder. Select that exact subfolder in Syncthing and share it with the other device.
3. To protect this workspace, first unlock your local vault, then choose Protect workspace and set a workspace password. Wait until encryption finishes before syncing the folder. On the receiving device, unlock its local vault and choose Unlock workspace with that password.
4. On a receiving Android device, let Syncthing finish receiving the folder, then choose Connect workspace folder and select the folder containing cqs-library.json. On Windows use the same Connect workspace folder action.
5. Activate the workspace and choose it under New notes → if you want new notes saved there. Move or copy existing notes into it using their workspace metadata controls. Selecting On this device only creates a private workspace that Syncthing cannot access.
6. Enable Watch external files. Android may suspend CQS in the background; reopening it scans incoming changes. Complete updates and compatible edits merge automatically by default, including edits made offline. Actual overlaps follow the selected conflict policy. Use the History refresh icon for an immediate scan.
7. If folder access is revoked or the storage is unavailable, cached notes are retained and shared writes stop. Restore storage access, then use Connect workspace folder to select the same workspace again. Do not select a different or empty replacement folder.

> Share the workspace manifest, notes tree and encrypted workspace key bundle when present. Device vault files and biometric credentials stay private. CQS uses a private mirror and immutable history to retain accepted changes and reconcile incoming edits. Grant the same folder separately in Calcite and your sync app.
