# PRD001 — Editor tweaks

**Status:** Proposed  
**Date:** 2026-10-13  
**Surface:** the editor in the Windows app. The same code runs on web.

Necessity is 0–10. 0 means leave it. 10 means it blocks normal writing. Scores come from using the Windows build, then checking the current source.

This slice does not change MyST, `mdoc`, or the rule that the Markdown string is the file.

## Do

| # | Tweak | Necessity | Change |
| --- | --- | --- | --- |
| 1 | Bold and italic shortcuts | 9 | Ctrl+B and Ctrl+I toggle once while the caret is in the document. The ribbon buttons stay as they are. |
| 2 | Space between pages | 7 | After save and reopen, the page the user saw comes back. Blank lines the user typed stay in the file. Page breaks stay `:::{page-break}`. |
| 3 | Rulers on a narrow window | 6 | Top and side rulers stay visible down to the desktop minimum width (900px), including a window snapped to half of a 1920px screen. |
| 4 | New project | 4 | One command creates a folder and one starting `.md`, then opens that file. |
| 5 | Next document tab | 4 | Ctrl+Tab and Ctrl+Shift+Tab move across document tabs. Tab inside a table still moves to the next cell. |

## Do not

| Idea | Necessity | Reason |
| --- | --- | --- |
| Tab label follows the file name on save | 3 | The tab shows the Title property, and it updates when Title changes. Save of a titled file has nothing to rename. Tried again; not repeatable. |
| Heading shortcut marks only the selected words | 3 | A heading is a paragraph style. Ctrl+1 and Ctrl+2 should keep styling the whole paragraph. |
| File name becomes the H1 | 2 | The large line on the page is the Title. The file name stays the file name. Writing it into the body would change the document on every rename. |

## Shortcut keys fire once

Ctrl+B runs `toggleBold()` in the window handler and again in the editor, so the mark appears and disappears in one keypress. Ctrl+I does the same. Ctrl+Alt+1 is the same pair of handlers on Heading 1.

**Done when:** with the caret in the document, Ctrl+B turns bold on, and a second press turns it off. Same for italic. Ctrl+1 and Ctrl+2 still style the whole paragraph. Ctrl+Alt+1 styles Heading 1 once.

**Drawbacks.** A handler that stops the key can also swallow the shortcut while a dialog or the source editor is focused. Source view has no bold command.

**Edge cases.** Focus on the ribbon. A collapsed selection. Bold already on part of the selection. Underline already calls `preventDefault` and must not start double-firing.

## Page spacing survives reload

ADR-005: the file stores an explicit page break only. Visual page frames are decorations. ADR-001: an open file that the user does not edit saves byte-identical.

Empty paragraphs are real text. A later full serialize must not collapse several of them into one gap. Decorations that were never written to the file must be computed again on open, so the page boundary the user saw is back without inventing newlines.

**Done when:** a document with a `:::{page-break}` reopens with that break. A document with blank lines the user inserted reopens with those lines. Opening and saving with no edits does not change the bytes.

**Drawbacks.** Markdown cannot store an arbitrary number of empty paragraphs as distinct blocks if we fall back to a full serialize. The fix belongs in the patch path, not in fake page-break newlines.

**Edge cases.** Windows CRLF on open. A break at the start or end of the body. Split view, where source and document must show the same bytes.

## Rulers follow the desktop window

Rulers are hidden below the `lg` breakpoint (1024px). The Windows window may be 900px wide, and half of a 1920px screen is about 960px. Margins remain editable in the properties panel either way.

**Done when:** at 900px and at 960px, the top ruler and the side ruler are on screen and still drag margins. Below the desktop layout, the phone chrome stays without rulers.

**Drawbacks.** Tick labels get tight. The page padding that clears the rulers (`38px` / `46px`) must exist whenever the rulers exist.

**Edge cases.** Zoom. A window dragged across the 1024px line. The custom Windows title bar (40px) must not cover the top ruler.

## New project

New folder creates an empty directory. New file creates one `Untitled.md` only after a folder is open. Neither creates a folder and a starting file together.

**Done when:** the user names a project, picks a parent folder, and lands in a new folder that contains one `.md` already open. Cancel leaves the disk unchanged.

**Drawbacks.** "Project" and "workspace" can be read as two different things. This command is a folder plus one file, not a new file format.

**Edge cases.** Name already taken. Empty name. Characters Windows rejects in a folder name. The parent picker dismissed.

## Ctrl+Tab moves between documents

Document tabs change on click. Nothing binds Ctrl+Tab. Tab inside a table moves cells and can show the Table ribbon.

**Done when:** two open documents, Ctrl+Tab selects the other, Ctrl+Shift+Tab selects the previous. One open document: the shortcut does nothing. Caret in a table: Tab still changes cell, Ctrl+Tab still changes document.

**Drawbacks.** People who learned Ctrl+Tab as "insert a tab in a table" from Word will get a document switch instead.

**Edge cases.** A preview tab. The find box or a dialog focused. Wrap from the last tab to the first.

## Out of scope

No installer change. No mobile layout. No new Markdown syntax. No rewrite of Title into the file name.
