# Keep a copy. Control access.

Understand what a lock protects, what an export contains, and how to keep a recoverable library.

<a id="local"></a>

## Know where your work goes

Calculations and notes live on your device. The app has no built-in cloud synchronization or telemetry. Optional semantic search can download a model or import it from disk; indexing and queries stay local.

> An MCP client is a separate application. If you authorize it to read your library, its handling of retrieved content—including any external model service—depends on that client.

[Workspace, vault and note keys explained](keys-and-encryption.md)

<a id="vault"></a>

## Set up the local vault

In Settings → Security, create a local vault on each device and save its recovery key. Windows currently uses a master password. Android offers a master password, biometrics / the device PIN, or both. This local vault protects the keys saved on that device.

- To give a workspace its own shared key without encrypting every note, open Workspace keys here in Security and choose Create workspace key. The local vault stores it securely. Use it for workspace QR transfers or select it under a note’s Encryption & signing controls; other notes remain plain.
- To protect an entire workspace, open Settings → Files & sync → Workspaces and choose Protect workspace. Set and confirm a workspace password. CQS encrypts current notes, attachments, version history, Trash and its managed recovery copies, then protects future notes in that workspace automatically.
- Each workspace key is an independent random 256-bit shared key, separate from your device’s personal access key. Creating one does not protect notes automatically. Whole-workspace protection reuses that workspace key and wraps it with a workspace password for folder synchronization.
- You may use the same password for your local vault and workspace, but they are separate settings. Changing one does not change the other. Save the workspace password before syncing.
- Workspaces you have not protected retain selective per-note encryption. Their new notes are plain until you protect them. An authorized MCP grant has separate access; locking the UI does not revoke that grant.

> Workspace encryption protects note contents, not calculator data, workspace names, filenames, sizes, timestamps or ordinary exports. Previously exported files, external backups and copies already held by a sync provider are not retroactively erased or encrypted.

<a id="library-keys"></a>

## Open one protected workspace on PC and phone

The shared workspace carries cqs-workspace-vault.json, a password-encrypted bundle for that workspace only. It never contains your local vault master key, plaintext keys, recovery key, biometric enrollment or private signing identities.

1. On the PC, create and unlock your local vault. Create or select a workspace, then use Protect workspace in Files & sync. Wait for protection to finish before sharing it.
2. For folder sync, transfer the complete workspace: cqs-library.json, cqs-workspace-vault.json and notes/. On the phone, create and unlock its own local vault, connect that folder, and choose Unlock workspace with the workspace password.
3. For QR, unlock the vault on both devices. Send the protected workspace using Password encryption. The receiving device uses that transfer password to unwrap an additional encrypted copy of the workspace key after you approve Import and merge. No plaintext key is put into a QR code.
4. Once imported, the workspace key is stored inside the receiving device’s local vault. Unlock that vault with its master password or Android device unlock to use the workspace again. Notes edited on either device remain encrypted with the same workspace key.

> A biometric-only phone vault can receive and use a password-protected workspace. To create a portable workspace on the phone, choose Protect workspace and give that workspace a password. Its biometric credential stays on the phone. Unprotected workspaces and older per-note encryption still need the matching keys transferred through Encrypted key backup & transfer.

[QR sending, receiving and conflict review](qr-transfers.md)
[Shared folder setup](sync.md)

<a id="unlock-methods"></a>

## Switch passwords and device unlock

Unlock the local vault before adding or changing its master password, or configuring device unlock. Android hides normal password changes while locked; the separate recovery-key route remains available.

- To add biometrics to an existing password vault on Android, unlock it with its master password and enroll device unlock. Both methods open the same local keys. Fingerprints or device PINs are never synced.
- To stay with a master password, keep using it and remove device unlock while unlocked. A biometric-only local vault must have a master password added before device unlock can be removed. After adding or changing the local master password, enroll device unlock again.
- Change a workspace password from that workspace’s controls in Files & sync. This rewraps the same workspace key; it does not rewrite notes, change your local master password, or require biometric re-enrollment. Devices with a cached key continue working. New devices use the updated workspace password.
- Old key bundles and backups retain their old passwords. Changing a password cannot revoke keys another device already holds. Keep an encrypted key backup and a separate recovery copy; a local recovery key does not by itself unlock another device’s workspace bundle.
- Auto-lock pauses while a QR transfer is being prepared, sent, received or reviewed. The full timer restarts when its native session ends. Close or cancel an abandoned transfer. Manual locking, closing the app or backgrounding a transfer still ends it.

> Desktop biometrics, Windows Hello, YubiKeys and platform authentication options for macOS/Linux may be added in the future. They are not available in the current desktop build. The workspace-key format keeps these future local unlock methods separate from synchronized encryption.

<a id="backup"></a>

## Keep a separate backup

In Settings → Files & sync, choose All — full library backup → Back up to folder. This combines accepted notes from every connected workspace, including inactive ones, with this computer’s state and encrypted key files in a new backup directory, including a manifest and RESTORE.txt.

- Protected content stays encrypted; plain content stays plain. A folder backup does not automatically encrypt the whole library.
- Notes, images & keys saves the note library and workspace identity/settings while excluding calculator history, variables, and graphs.
- Unsaved drafts and unaccepted incoming changes are excluded. Save and review them first.
- Semantic caches and MCP grants are excluded from normal library backups.
- Keep backups outside selectively shared workspace folders. A backup can include General even when only Phone is shared.
- Protected workspace backups include their encrypted workspace key bundles. Complete pending workspace encryption before backing up. Keep the workspace password as well as your local vault recovery material.

<a id="exports"></a>

## Match the export to its purpose

Markdown and plain text are useful in other editors; JSON preserves structured note data. Ordinary exports are readable. For a maintained encrypted copy, use Live encrypted export with your vault key or selected contacts. Folder backups retain each file’s existing protection.

> Encrypting the library does not make ordinary exported text private. A signed bundle proves integrity and origin; signing alone does not encrypt its contents.

[Live encrypted exports: keys, recipients, and recovery](note-exports.md)

<a id="restore"></a>

## After restoring

Follow RESTORE.txt with Calcite closed. Restore the primary profile, unlock its restored keys, then copy each additional workspaces/<workspace-id> folder to an empty location and connect it through Workspaces. Do not overwrite a live synced folder. Choose active workspaces and a default destination on each device. Verify the restored notes, rebuild semantic search if needed, and create fresh MCP grants on another installation. Restore cqs-workspace-vault.json with its workspace when present. Unlock the restored local vault, then use the workspace password if its key is not already cached.

> Note version history and trash help recover edits or deletions inside the app. Keep external backups for device loss, storage failure, and broader recovery.
