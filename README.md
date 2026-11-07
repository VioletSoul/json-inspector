# JSON Inspector 4.3 · Graphite Studio

![Version](https://img.shields.io/badge/version-4.3-548af7?style=flat-square)
![HTML](https://img.shields.io/badge/HTML-single_file-e34f26?style=flat-square)
![CSS](https://img.shields.io/badge/CSS-custom_styles-1572b6?style=flat-square)
![JavaScript](https://img.shields.io/badge/JavaScript-vanilla-f7df1e?style=flat-square)
![Web Workers](https://img.shields.io/badge/Web_Workers-background_processing-8b5cf6?style=flat-square)
![Offline](https://img.shields.io/badge/offline-ready-6aab73?style=flat-square)
![Languages](https://img.shields.io/badge/interface-EN%20%7C%20RU-8b9ebd?style=flat-square)
![Dependencies](https://img.shields.io/badge/runtime_dependencies-0-6aab73?style=flat-square)

[English](README.md) · [Русский](README.ru.md)

A local-first JSON inspector in one HTML file, with the Graphite Studio interface. Explore trees and tables, edit source, inspect exact values, and compare documents without installation, external libraries, or a server.

Built with HTML, CSS, and vanilla JavaScript. Web Workers handle parsing, formatting, and comparison. Python and Node.js are not part of the application and are not required to run it.

The dark palette is inspired by developer tools such as IntelliJ IDEA. This is an independent project.

## Quick start

1. Save `JSON-Inspector-4.3-Graphite.html` to your computer.
2. Open it in a modern browser.
3. Click Open, drop a JSON file into the window, or paste JSON into JSON Source and click Apply.
4. Use Sample to explore the built-in document.
5. For comparison, apply the primary document, open JSON Diff, and load a second file.

No build step, package manager, or local web server is needed. The application runs from a local `file://` page, subject to browser security settings.

Background tasks require Blob-based Web Workers. If the browser blocks Worker startup, an error is displayed; there is no silent fallback to synchronous parsing.

## What's new in 4.3

Graphite Studio redesigns the workspace while retaining the 4.2 computational core: parsing, numeric-token preservation, exact comparison, and document limits remain unchanged.

- Lighter graphite surfaces, larger controls and text, softer corners, and more spacing.
- A centered welcome screen with file opening, a sample, and an editor shortcut.
- Search beside the view tabs, collapsible options, and removable active-filter buttons.
- A docked right-hand inspector that does not overlap the document on desktop.
- A mouse- and keyboard-adjustable inspector divider; a bottom-sheet inspector on narrow screens.
- Comfortable spacing by default, with a Compact / Comfortable density toggle.
- A document-state badge alongside the detailed state text.
- Statistics moved below the workspace.
- Inline SVG icons without icon libraries or external assets.
- Reduced-motion styling that disables interface transitions when requested by the browser setting.

Background processing, cancellable tasks, draft protection, Diff, reports, error navigation, and English/Russian switching remain available.

## Features

| Area | Capabilities |
| --- | --- |
| Tree | Expand/collapse, exact-node selection, pagination, keyboard navigation |
| Tables | Records for arrays of objects; Paths for leaves and empty containers |
| Search | Keys, values, paths, type filter, exact match, case sensitivity, next/previous match |
| Navigation | Dot/bracket paths, ancestors, parent navigation, reveal on the correct tree page |
| Inspector | Type, depth, child count, line/column, path, original fragment, full-subtree copying |
| Source | Editing, line numbers, highlighting, synchronized preview, formatting, minification |
| Diff | Added, Removed, Modified, Unchanged; filters, search, pagination, details, JSON report |
| Numbers | Original tokens preserved; exact decimal-value or token comparison |
| Unicode | Unpaired-surrogate warnings without silent repairs |
| Export | Copy source/path/subtree and download JSON without editing the input file in place |
| Interface | Graphite Studio; English/Russian controls and diagnostics; comfortable and compact density |

## Working with documents

### Draft, applied document, and export

These are separate states:

- The source editor holds the current draft.
- Apply parses the draft and updates the tree, tables, inspector, and statistics.
- Format and Minify change the draft but do not apply it automatically.
- Copy copies the current source text, including an unapplied or invalid draft.
- Download first applies and validates the current source, then requests a JSON download.

Switching tabs or interface language does not replace the editor's draft with the applied document.

A parsing error leaves the last applied document available. The state bar explains when the views differ from the draft. File replacement and page-close warnings help prevent accidental loss, subject to browser dialog behavior.

A download request does not prove that the browser saved the file. Verify the result. The original input file is never edited in place.

### Tree, tables, and inspector

Click a tree value to inspect it. Use ↗ to open a container, or a value's parent, in a table. In tables, click values to inspect them and ↗ beside a container to drill into it.

Records combines object properties into columns. Missing properties appear as `—`; JSON null remains a value. Paths lists leaves and empty containers below the selected table root.

The inspector supports Parent, Copy Path, Copy Subtree, and pinning. On desktop it is docked to the right; drag the divider or focus it and use the keyboard to change its width. On narrow screens it opens as a bottom sheet that can overlap the lower part of the workspace; close it to restore the unobstructed view. Long previews are truncated; copying uses the complete original fragment. Breadcrumb buttons navigate to ancestors. Pinning keeps the inspector open when applying a document; Close and Esc can still dismiss it.

Internal node relationships use numeric indices, not display paths.

## Search and paths

Enable Keys, Values, Paths, or a combination. Enabled areas are combined with OR; the optional type filter restricts results to one type.

Exact match compares an entire individual search field. Case sensitive preserves case distinctions. Search is literal, not regular-expression based; case-insensitive matching is not full Unicode case folding.

Strings are searched decoded, numbers by original token, booleans/null by JSON text. Object/array value search uses the type name rather than serialized contents.

Use ↑ / ↓ beside the search field to move between matches. Expand Search options to change the search scope and matching rules. Click an active-filter button marked × to disable that option. Reset search clears the query and options. Search runs in the main thread with a short input delay.

Supported paths:

```text
$
$.users[0].name
$["users"][0]["name"]
$["key.with.dot"]["hello world"]
```

Canonical paths quote object keys using JSON syntax. Use bracket notation for punctuation, spaces, Unicode, and unusual names. This is not a full JSONPath engine: wildcards, filters, recursive descent, slices, and executable expressions are unsupported.

## JSON Diff

Diff compares the last applied primary document with a separately loaded comparison document. Apply source edits before comparing them.

| Status | Meaning |
| --- | --- |
| Added | Only in the comparison document |
| Removed | Only in the primary document |
| Modified | Type, value, or descendant content differs |
| Unchanged | Equal under the selected rules |

An ancestor becomes Modified when its descendants change. The interface separately identifies changed descendants. Counts refer to nodes, including containers, not independent editing operations.

### Comparison rules

- Objects are matched by decoded key, ignoring property order.
- Arrays are matched by index, without move or shift detection.
- Strings are compared by decoded value, not escape spelling.
- Formatting whitespace does not affect semantic equality.
- Duplicate decoded keys are rejected in both documents.

Exact numeric value mode normalizes decimal coefficients and BigInt exponents without conversion to JavaScript Number. Equivalent decimal/exponential representations compare equal. A separate option distinguishes negative and positive zero.

Number lexeme mode compares original spelling, including notation and trailing zeros; signed-zero spelling remains distinct in this mode.

### Using Diff

- Load a second file and use Swap sides to exchange documents. Swapping replaces the primary editor content and prompts when necessary.
- Refresh after applying a changed primary document or changing number-comparison settings. Stale results are not presented as current.
- Use Only differences, the status filter, search, and pagination.
- Move to the next/previous change.
- Click a path to expand side-by-side fragments and descendant entries. Long fragments and descendant lists are capped for display.
- Click a value to copy its full original fragment; use ↗ to reveal a primary node in the tree.

### Report export

Export report downloads JSON containing file names, comparison settings, counts, node statuses, original document texts, and node ranges. Ranges use zero-based UTF-16 offsets with exclusive ends.

Each source document is included once; rows reference ranges instead of repeating whole subtrees. The report contains the full original documents: treat it as sensitive if the inputs are sensitive.

The report includes all comparison rows, not just the currently filtered page. It describes the applied documents, not an unapplied draft. Exporting does not modify either input.

## Data integrity

### Exact numeric tokens

Formatting, minification, and copying preserve values such as:

```json
{
  "largeId": 9007199254740993,
  "precise": 0.123456789012345678901,
  "negativeZero": -0,
  "largeExponent": 1e999
}
```

Numbers are retained as text. Exact comparison does not expand large exponents into decimal strings. Downstream tools may still be unable to represent these values accurately.

### Invalid input and Unicode

Duplicate decoded keys are rejected rather than overwritten: `"a"` and `"\u0061"` count as the same key. This is an intentional application policy.

The parser expects JSON, not JSON5 or JSON Lines. Comments, trailing commas, NaN, Infinity, malformed escapes, and unescaped control characters are rejected.

Unpaired UTF-16 surrogates produce warnings without repairing or normalizing the source. Warning entries identify the string token's start and an offset within the decoded string; they do not identify the exact source spelling of each surrogate. The warning list displays up to 100 entries.

Files are decoded as UTF-8; malformed byte sequences are not separately validated. A leading BOM is removed when opening either file.

Error diagnostics show a line excerpt and offer editor navigation when the error belongs to the current editor text.

## Background work and performance

Parsing, formatting, and comparison have separate Worker services. Starting another task in the same service cancels its previous task; cancelling formatting does not automatically cancel comparison.

Worker services are reused. Comparison caches its active inputs; restarting that service clears its cache. Results are checked against document versions before use.

Search, source highlighting, selectors, model transfer, report preparation, and DOM rendering still involve main-thread work. Background processing does not guarantee a completely non-blocking interface.

## Keyboard and layout

| Key | Action |
| --- | --- |
| Cmd + Enter / Ctrl + Enter in editor | Apply |
| Tab in editor | Insert two spaces |
| Enter in path field | Go to path |
| Up / Down in tree | Previous / next visible node |
| Right in tree | Expand or move to first child |
| Left in tree | Collapse or move to parent |
| Home / End in tree | First / last visible node, across pages |
| Enter / Space in tree | Open selected node's inspector |
| Left / Right on view tab | Switch tabs |
| Home / End on view tab | First / last tab |
| Left / Right on the inspector divider | Widen / narrow the inspector by 10 px, within available space |
| Shift + Left / Right on the divider | Adjust inspector width by 40 px |
| Home / End on the divider | Minimum / maximum allowed inspector width |
| Esc | Close inspector and exit data-focus mode |

Search options can be collapsed; the Statistics button toggles the strip below the workspace. Data focus hides secondary panels; Show panels restores them.

Comfortable density is the default. Click Compact to reduce spacing, then Comfortable to restore it. Density and manual inspector sizing are not persisted across page reloads.

The inspector divider is available in the desktop layout. On narrow screens the inspector becomes a bottom sheet without a horizontal resize handle. At viewport widths of 1250 px or less, the separate highlighted-source preview is hidden to prioritize the editor; this does not alter the JSON or disable editing.

The interface has a minimum layout width of 360 px. Responsive styling is not a claim of complete mobile usability. It uses modern CSS features including `:has()` and dynamic viewport units.

Clipboard behavior depends on browser permissions. If copying fails, select source text and use your normal copy shortcut.

## Limits

| Resource | Limit |
| --- | ---: |
| Input / formatted output | 5 MiB |
| Nodes per document | 100,000 |
| Nesting depth | 150; root depth is 0 |
| Combined canonical paths | 8,388,608 UTF-16 code units |
| Tree page | 250 nodes |
| Table / Diff page | 100 rows |
| Records property columns | 40, plus index |
| Highlighted source | 200,000 UTF-16 code units |
| Inspector preview | 100,000 UTF-16 code units; full-value copying |

Use Paths to access record properties omitted by the column limit. Source highlighting and line numbers turn off for large drafts; editing remains available.

Limits reduce resource use, not guarantee response time or a fixed memory ceiling.

## Privacy and security

- Local processing, no application network requests or runtime dependencies.
- No document persistence in localStorage, IndexedDB, or cookies.
- User data is inserted through text DOM operations, not HTML interpolation.
- Object lookup uses Map; paths are not executed as code.
- CSP blocks network connections, embedded objects, and frames; inline scripts/styles and Blob Workers are allowed.
- Clipboard, JSON download, and report export are user-initiated.

Local processing does not protect against compromised browsers, extensions, operating systems, or modified HTML. Handle exported reports and clipboard contents according to your requirements.

Shields.io badges belong to this documentation only; the HTML does not load them.

## Language

Click EN or RU to switch to the indicated language. The initial language follows the browser: Russian for a Russian setting, English otherwise. Switching does not change your JSON or file names. The preference is not persisted.

## Validation

Recorded development checks for the 4.3 Graphite Studio build include:

- 122 computational-core checks using functions extracted from the generated HTML.
- Verification that the computational-core block remained unchanged from the supplied 4.2 build.
- JavaScript syntax checks and checks for missing or duplicate DOM IDs.
- Browser scenarios in headless Chromium covering the welcome screen, draft retention, formatting, language switching, removable filters, docked inspection, keyboard resizing, density switching, tree navigation, Diff, report download, side swapping, error navigation, and data-focus mode.
- Layout inspection at several viewport sizes and a separate check of the mobile inspector sheet with a valid applied document.

The 161 core checks and 30 browser checks previously documented for 4.2 are historical results for that build, not counts for 4.3.

No Node.js command or separate test file is required to use the application. These are recorded development results, not a guarantee for modified copies.

This is not comprehensive cross-browser or accessibility certification. Other browsers, screen readers, system clipboard integration, drag-and-drop, and visual usability still need further manual testing. Narrow-screen behavior, particularly with diagnostics and the inspector open together, needs additional usability review.

## Repository files

```text
.
├── JSON-Inspector-4.3-Graphite.html
├── README.md
├── README.ru.md
└── LICENSE
```

Only the HTML is needed to run the application.

## License

MIT. See LICENSE.
