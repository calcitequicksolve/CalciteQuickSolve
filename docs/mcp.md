# Connect an agent to your workspace.

Enable the local MCP server, issue a credential, and configure a client using the app’s own connector.

<a id="prepare"></a>

## 1. Prepare the desktop app

Set up your vault in Settings → Security and unlock its keys. Open Settings → MCP, enable the MCP server, and keep the desktop app running. It can remain in the tray.

> MCP is off by default. There is no public HTTP or SSE endpoint, and no Docker service is needed.

<a id="grant"></a>

## 2. Create an agent grant

Enter an agent name, choose permissions, and optionally set an expiry. Select Generate agent PEM, then Save PEM file.

- Save the credential before dismissing it. It is shown only in the current settings session.
- Store it privately, outside shared or synced app-data folders.
- If you lose it, generate a fresh grant and revoke the old one. It cannot be recovered from the UI.

<a id="connect"></a>

## 3. Configure the client

Enter the saved PEM’s absolute path in Settings and copy the displayed mcpServers configuration into your MCP client. The example below shows the shape; replace both paths with the real paths from your installation.

> The same executable runs --mcp-connect without opening a second UI. The connector speaks stdio MCP and connects to the running app over authenticated, encrypted loopback transport. Your client’s exact configuration location depends on that client.

<a id="permissions"></a>

## Choose permissions for the task

Permissions apply to the grant’s workspace and its keys, including future keys. Activating other workspaces in the UI does not expand the grant. Permissions do not restrict a grant to an individual note.

- Read library: retrieve notes, attachments, versions, trash, calculation data, search results, exports, and encrypted backups.
- Edit library: create or edit notes and attachments, and commit calculations, variables, functions, and graphs.
- Delete & restore: trash, restore, or purge notes and remove calculation history.
- Sign, encrypt & decrypt: use CQS keys for cryptographic operations and secure note exchange. Changing note protection also requires Edit library.
- Manage CQS keys: generate, import, rename, archive, trust, or choose storage keys. Deleting keys also requires Delete & restore.
- Export private keys: export private keys protected by an agent-chosen passphrase. The agent can recover those keys.
- Manage other agents: create credentials, change any grant’s permissions, revoke grants, and configure the server. This can grant full access to itself or others.
- Settings, import & backup: change app settings and perform related import, backup, and index operations. Replacement imports also require Edit and Delete.
- Administer vault: change auto-lock or the master password. Treat this as authority comparable to private-key export.

<a id="lifecycle"></a>

## Locking, expiry, and revocation

Locking the app UI does not stop authorized agents. Their grants can continue working after an app restart without your master password. Disable MCP or use Revoke access to stop subsequent requests; a request already in progress may finish.

- You can disable the server or revoke a grant while the UI is locked.
- Adding, restoring, or changing permissions requires the UI vault to be unlocked.
- Expiry also stops subsequent requests.
- Changing the vault password does not revoke grants.
- Revocation cannot recall exported content or keys, or automatically revoke other grants an agent created.

<a id="credential"></a>

## Understand the credential boundary

A PEM is both an authentication credential and decryption authority. Normal API responses do not return private keys without explicit export permission, but a PEM together with access to the app’s grant and key-store files can permit offline decryption outside API permission checks.

> This connection is not hardware isolation from malicious code running as your local user. Keep the credential private and grant administrative or export permissions only when the workflow needs them.

<a id="troubleshoot"></a>

## Check a connection

Confirm that the main app is running, MCP is enabled, the executable and PEM paths are absolute, and the grant is neither expired nor revoked.

- After moving the executable, update the client’s command path.
- After changing the local server port, issue a fresh credential with the new connection metadata and revoke the old one.
- Review Recent agent activity for tool names, outcomes, grant names, and times. The log retains the latest 1,000 operations without request payloads.
- Agent credentials and grants are device-local and excluded from ordinary library backups. Create fresh grants after restoring to a new installation.

<a id="note-files"></a>

## Automate note files and live exports

The file workflows are also exposed through MCP. Supply file bytes as base64; these tools do not give an agent an arbitrary filesystem-read command.

1. Read the note and its current revision before making a cursor-based insertion. position and end count UTF-16 code units.
2. Configure an absolute destination path in an existing directory with cqs_live_export_set. This applies to subsequent saves; it does not save a dirty UI draft.
3. Save through cqs_note_save, or call cqs_live_export_now to write the latest saved version. Inspect returned errors or liveExportErrors even when the note save succeeded.

| Tool | Purpose / permissions |
| --- | --- |
| cqs_note_import | Import a named file as a new note. Requires Edit library; encrypted input also needs Sign, encrypt & decrypt. |
| cqs_note_insert_file | Insert text or an attachment at position/end UTF-16 offsets, with expectedRevision. Requires Read and Edit library; stale revisions are rejected. |
| cqs_note_asset_read / cqs_note_asset_write | Read or write exact attachment bytes, including empty files, with filenames, MIME types, and SHA-256. |
| cqs_note_export | Export JSON, MD, or TXT; encrypted and recipientIds select secure outputs. Requires Read library and, for encryption, Sign, encrypt & decrypt. |
| cqs_live_export_get | Read a note’s device-local destinations, errors, output paths, and timestamps. Requires Read library. |
| cqs_live_export_set / cqs_live_export_now | Configure a target or refresh it from the latest saved note. Requires Settings, import & backup, Read library, and Edit library; encrypted targets also require Sign, encrypt & decrypt. |

> Unlike the UI’s Update exports now button, the MCP refresh command uses the latest saved note and does not save a dirty UI editor. An empty recipientIds list uses the current vault key.

[Full live-export setup guide](note-exports.md)
