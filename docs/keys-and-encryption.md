# Workspace keys, vault keys and note keys

What each key protects, how devices unlock them, and how to transfer a workspace without sharing your whole vault.

<a id="overview"></a>

## Three different jobs

A key is secret data used by encryption. A password or Android biometrics unlocks access to keys; it is not the workspace key itself. These protections are available in the native Windows and Android apps.

| Key | What it does | Where it belongs |
| --- | --- | --- |
| Vault master key | Encrypts the private key store on this device. Your master password wraps it; Android device unlock can also grant access. | Device-local vault; never included in a workspace QR transfer. |
| Workspace key | An independent random AES-256 shared key bound to one workspace. Encrypts workspace QR transfers and optionally selected notes or the entire workspace. | Stored inside your encrypted local vault; shared only by an explicit password-protected key transfer or protected-workspace bundle. |
| Note / content key | A fresh random AES-256 key encrypts each protected record. Recipient wrappers allow the chosen workspace key, shared keys or private identities to unlock that record. | Stored only in encrypted, wrapped form with the protected record. New protected writes use fresh content keys. |

<a id="vault"></a>

## Your local vault unlocks the key store

Setting up Security creates a device-local vault and a personal access key. The vault master key protects the key store containing shared keys and private identities. Windows unlocks it with the master password; Android can use the master password, biometrics / device PIN, or both, depending on your settings. Each device has its own vault.

- The master password wraps the vault master key. Changing it does not change every workspace or note key.
- Android biometrics authorizes device-backed unlocking; your fingerprint is not used as a workspace encryption key.
- A recovery key helps recover the local vault, but still requires its vault files. Keep a complete backup.
- Notes storage / personal access keys are device-vault keys. They are not a fallback for a workspace QR transfer. Private signing/decryption identities are separate keypairs.

<a id="workspace"></a>

## One shared key per workspace

A workspace key is a symmetric shared secret, not a public/private keypair. Creating it does not enable Protect workspace and does not encrypt existing plain notes. It stays bound to its own workspace.

- Newly created workspaces get a key automatically when your vault exists and is unlocked, including a newly imported workspace. An incoming transferred key is used instead of generating a replacement.
- If your vault is locked during creation, choose Unlock now or explicitly create without a key. Create the missing key later in Settings → Security → Workspace keys. Without a configured vault, create one first.
- Connecting an existing folder does not generate a replacement key. For a protected workspace, unlock the existing key in Files & sync.
- In an unprotected workspace, leave some notes plain and choose its workspace key under individual notes’ Encryption & signing controls. Plain notes remain readable with the vault locked.
- Protect workspace is a separate all-note policy. It encrypts notes, attachments, history, Trash and managed recovery copies, and applies to future writes. Its password wraps the same workspace key into cqs-workspace-vault.json for use on another device.

<a id="note"></a>

## Note keys protect individual records

Each encrypted stored record uses a fresh random content key and authenticated AES-256-GCM encryption. That content key is wrapped for the selected recipient keys. A workspace shared key unlocks its wrapper; an identity recipient needs the matching private identity. Attachments and history records are protected too. There is no separate note-key password to manage.

- Selecting a workspace key for a note uses that workspace key without adding another workspace’s key or a device personal-access fallback.
- Plain notes have no encrypted content key. Creating a workspace key alone leaves them plain.
- Transport encryption and storage encryption are independent: an encrypted QR transfer can carry plain notes without making them encrypted on disk. Existing notes keep their local protection when merged.

<a id="transfer"></a>

## Choose how a QR transfer is unlocked

Password is the default QR encryption method. Use a transfer password when sending to a device that does not already have the workspace key. The receiver needs a configured, unlocked local vault before Begin; a red explanation appears when it cannot begin.

- Encrypt with Workspace key uses only that workspace’s existing key. It does not send the key. Both devices must already have the matching key.
- Transfer workspace key is an explicit checkbox beside history and Trash. It requires the sender’s vault to be unlocked and forces transfer-password encryption. Both the key bundle and the complete transfer are password encrypted.
- The checkbox sends only that workspace’s shared key. It never sends your PC’s vault master key, vault recovery key, personal access key, Android biometric enrollment, or private identities.
- Existing workspaces with notes or keys reject incoming workspace keys. Turn off Transfer workspace key and use Password to merge. A completely empty, keyless, unprotected non-primary workspace left by an unfinished import can accept a retry; existing keys are never replaced.
- Add workspace selects a name and storage location for the current transfer. After selecting a shared folder, click Add workspace again. The actual workspace folder and connection are created only when Import is pressed.
- Cancelling after data arrives asks for confirmation, even without conflicts. Return to Import to keep reviewing, or discard the transfer and scan again. Cancelling before Import leaves no new workspace folder.

<a id="backup"></a>

## Keep the keys needed to recover your data

Keep a complete library and local-vault backup, the vault recovery key, and any needed workspace passwords in a safe place. A workspace folder with plain notes does not automatically carry its independent workspace key. Explicitly transfer that key with a password, keep an encrypted key backup, or use the password-wrapped bundle of a protected workspace.

> Encryption does not hide workspace names, filenames, sizes or timestamps, and does not retroactively protect ordinary exports or copies already held elsewhere.

[Security & backups](security-and-backups.md)
[QR transfers](qr-transfers.md)
[Workspace folders & sync](sync.md)
