# Open the app from a link.

Open License settings, calculations, Notes or Search from your browser, and set up links for a standalone Windows copy.

<a id="setup"></a>

## Enable browser links on Windows

The current Windows installer registers CalciteQuickSolve links for your Windows user, including when installing an update. For a fresh setup with no existing app data or link registration, the standalone app registers itself on its first production launch.

1. Put the standalone EXE in its permanent folder and launch it once before using a browser link.
2. Existing users keep their current registration state when launching a newer standalone EXE. If links are disabled or automatic setup failed, open Settings → General → Desktop and turn on Enable browser links. This change applies immediately, without Save settings.
3. If you move the EXE, open the copy at its new location and choose Repair browser links. Refresh status checks the current registration.
4. Turn off Enable browser links to disable this copy's registration; it stays off on later app launches. Running the installer registers its copy again. If an older location remains registered, use Disable browser links when it is offered.

> Automatic setup keeps any existing browser-link registration. Use Enable or Repair to make a different copy the handler.

<a id="destinations"></a>

## Choose where a link opens

Supported links open the main window, including when the app is minimized or hidden in the tray. A link received during startup waits until the app is ready.

| URL | Destination |
| --- | --- |
| calcitequicksolve://settings/license | Settings → License. This exact link only navigates; it carries no activation or customer information. |
| calcitequicksolve://open/calc | The full calculator workspace. |
| calcitequicksolve://open/calc/2%2B2 | The calculator with 2+2 filled in. Press Enter or Evaluate to save the calculation in History. |
| calcitequicksolve://open/notes | The full Notes workspace, when Notes is available. |
| calcitequicksolve://open/search | The Search page. |

> A calculator link can update the usual live preview; it does not submit the expression to History. If it would replace text already in Expression, the app asks before replacing it.

<a id="open-note"></a>

## Open an existing note by ID

Use the note’s stable UUID, shown beside ID in its editor. Its workspace must be connected on this computer. A link does not transfer a note or connect a missing workspace.

- Replace the example UUID with the ID of your note.
- Notes must be available through your trial, activation or retained read access. Protected notes still require the local keys and an unlocked vault.
- The editor's Copy link button produces an internal Markdown id: link. Use its UUID in the browser URL above.

```
calcitequicksolve://open/note/12345678-1234-4234-8234-123456789abc
```

<a id="create-note"></a>

## Create a note with an optional title and body

A create link saves a new note in this device’s default workspace using a new UUID. Creation requires active Notes editing access. An existing ID is rejected instead of overwriting the note, including when that note is in Trash.

- Generate a fresh UUID for each new note. Reopening the same create link does not create another copy.
- The body segment and query are optional. Without them, the note starts empty in plain text format.
- optionalname supplies the title. format accepts plain or markdown and defaults to plain. Unknown query options are ignored.
- Encode the body as one URL path segment: spaces become %20, line breaks %0A and slashes %2F. Encode the title separately as a query value.

```
calcitequicksolve://create/note/12345678-1234-4234-8234-123456789abc/Hello%20world/?optionalname=Meeting%20notes&format=plain
```

> A create link contains the text and title you put into it. Anyone who receives that URL can read those values; use an ordinary note export or encrypted sharing for private content.

<a id="drafts"></a>

## Keep drafts when following a link

Opening License settings preserves the current note and unsaved Settings fields. Reopening the same note does not reload over its draft.

- If a link opens or creates another note while the current note has unsaved edits, choose Save and continue or Cancel. A save error keeps you in the confirmation so the draft is not discarded.
- Links to another workspace view keep an open Settings form available when you return to Settings. Use Save to apply those settings.
- If an attachment or note save is still in progress, finish it and open the link again.

<a id="fallback"></a>

## If a link does not open the app

Your browser may ask permission to open CalciteQuickSolve. Canceling that prompt, missing registration or an older app can leave the page unchanged; the website cannot reliably tell which happened.

1. Launch CalciteQuickSolve manually. For activation, go to Settings → License and paste or import the activation from the page.
2. If your browser reports an unknown protocol, check Settings → General → Desktop → Enable browser links. Enable them if they are off, or choose Repair browser links after moving the EXE. Launching a newer standalone EXE does not automatically enable links for existing users.
3. Use the exact supported scheme, destination and UUID. Malformed links, invalid text encoding and unsupported routes are ignored. The License URL accepts no query, fragment or trailing slash.

[Activate Notes](licensing.md)
[Downloads and checksums](https://calcitequicksolve.app/releases/)
