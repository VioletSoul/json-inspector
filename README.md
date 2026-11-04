# JSON Inspector 3.0

![Version](https://img.shields.io/badge/version-3.0-548af7?style=flat-square)
![HTML](https://img.shields.io/badge/HTML-single_file-e34f26?style=flat-square)
![JavaScript](https://img.shields.io/badge/JavaScript-vanilla-f7df1e?style=flat-square)
![Offline](https://img.shields.io/badge/offline-ready-6aab73?style=flat-square)
![Languages](https://img.shields.io/badge/interface-EN%20%7C%20RU-8b9ebd?style=flat-square)
![Dependencies](https://img.shields.io/badge/runtime_dependencies-0-6aab73?style=flat-square)

[English](README.md) · [Русский](README.ru.md)

A compact, local-first JSON inspector in a single HTML file. Explore documents as a tree, browse tables, inspect values, and edit source without a server, installation, or external libraries.

The interface keeps controls small and gives most of the window to your data. Its dark palette is inspired by developer tools such as IntelliJ IDEA; this is an independent project.

## Quick start

1. Save `json-inspector-3.0.html` to your computer.
2. Open it in a modern browser.
3. Click **Open**, drop a JSON file anywhere in the window, or paste JSON into **JSON Source** and click **Apply**.
4. Use **Sample** to explore the built-in document.

No build step, package manager, Python, or local web server is needed. The application works from a local `file://` page.

## Features

| Area | Capabilities |
| --- | --- |
| Tree View | Expand/collapse branches, select exact nodes, paginated rendering |
| Table View | Records mode for arrays of objects; Paths mode for leaf values and empty containers |
| Search | Search keys, values, and canonical paths; move between matches with ↑ / ↓ buttons |
| Navigation | Resolve simple dot and bracket paths; reveal ancestors and the appropriate tree page |
| Value Inspector | Type, depth, direct-child count, canonical path, original value, full-value copying |
| JSON Source | Editable source, line numbers, highlighting, synchronized preview, formatting and minification |
| Statistics | UTF-8 size, nodes, properties, objects, arrays, strings, numbers, maximum depth |
| Export | Copy source or download JSON without overwriting the input file |
| Interface | Compact dark layout; complete English/Russian switching |

### English / Russian interface

Click **EN** to switch to English or **RU** to switch to Russian. The button displays the language you can switch to.

The switch updates controls, placeholders, accessible labels, table headings, type descriptions, diagnostics, status messages, confirmations, and the value inspector. It does not translate or modify your JSON, property names, values, or file names.

The initial language follows the browser: Russian for a Russian language setting; English otherwise. The selected language is kept only for the current page session. No language preference or JSON document is written to browser storage.

### Tree and tables

- In the tree, click a value to open the inspector. Use **↗** to open a container, or a value's parent, in the table.
- In a table, click a value to inspect that exact node; use **↗** beside a container to drill into it.
- **Records** combines properties from objects in an array into columns. Missing properties appear as `—`; JSON `null` remains a value.
- **Paths** lists leaf values and empty containers under the selected node.
- Search temporarily exposes matching branches and their ancestors. Clearing it restores the previous expansion state.
- Long previews show an excerpt near a match. The inspector can copy the complete original value.

### Paths

Supported examples:

```text
$
$.users[0].name
$["users"][0]["name"]
$["key.with.dot"]["hello world"]
```

Canonical paths use JSON-quoted object keys. Simple dot syntax accepts identifier-like names. Use double-quoted bracket syntax for spaces, punctuation, Unicode keys, or unusual names.

This is not a full JSONPath engine: wildcards, filters, recursive descent, slices, and executable expressions are not supported.

## Data integrity

### Exact numeric text

The parser retains numeric tokens as text instead of converting them to JavaScript `Number`. Formatting and minification preserve values such as:

```json
{
  "largeId": 9007199254740993,
  "precise": 0.123456789012345678901,
  "negativeZero": -0,
  "largeExponent": 1e999
}
```

These values are displayed and exported without numeric evaluation. The application does not perform arithmetic or guarantee that downstream tools can represent the same numbers.

### Duplicate keys and invalid input

Duplicate decoded keys are rejected rather than silently overwritten. For example, `"a"` and `"\u0061"` count as the same key. Diagnostics include a line and column.

The parser expects JSON, not JSON5 or JSON Lines. Comments, trailing commas, `NaN`, and `Infinity` are rejected. A leading BOM is removed when opening a file. File contents are decoded as UTF-8; malformed byte sequences are not separately validated.

A parsing error leaves the last applied document in the tree, table, and statistics. Edit the source and apply it again to update those views.

### Applying is not saving

**Apply** updates the views; it does not save the file. Changed text remains unexported until **Download** is requested. Replacement and page-close warnings help prevent accidental loss, subject to browser dialog behavior.

The page can request a download but cannot confirm that the browser completed it. Always check that the exported file was actually saved. The original input file is not edited in place.

## Keyboard controls

| Key | Action |
| --- | --- |
| `⌘ + Enter` / `Ctrl + Enter` in the editor | Apply JSON |
| `Tab` in the editor | Insert two spaces |
| `Enter` in the path field | Go to the path |
| `Esc` | Close the value inspector |
| `←` / `→` while a view tab is focused | Switch view tabs |
| `Home` / `End` while a view tab is focused | Select first / last view tab |

Clipboard access depends on browser permissions. If automatic copying is unavailable, select the source and use your normal copy shortcut.

## Limits

| Resource | Limit |
| --- | ---: |
| Input / formatted output | 5 MiB |
| Parsed nodes | 20,000 |
| Nesting depth | 100; root depth is 0 |
| Combined canonical-path length | 4,194,304 UTF-16 code units |
| Tree page | 250 nodes |
| Table page | 100 rows |
| Record-table property columns | 40, plus index column |
| Highlighted source | 200,000 UTF-16 code units |
| Inspector preview | 100,000 UTF-16 code units; copying uses the full value |

When a record table exceeds its column limit, the interface reports it. Use Paths mode to access omitted values. Highlighting and line numbers turn off for large documents; the editor remains available.

Parsing runs on the main thread. Conservative limits reduce resource use but cannot guarantee instant responses on every device. Search is case-insensitive substring matching, not regular-expression or full Unicode case-folding search.

## Privacy and security

- JSON processing runs locally in the browser.
- The application makes no network requests and has no runtime dependencies.
- No JSON is stored in `localStorage`, IndexedDB, or cookies.
- User data is inserted using text DOM operations, not HTML interpolation.
- Object-key lookup uses `Map`; path expressions are not evaluated as code.
- The Content Security Policy blocks network connections, embedded objects, and frames. Inline scripts/styles are allowed to keep the application self-contained.
- Clipboard and download actions are initiated by the user.

Local processing is not a guarantee against compromised browsers, extensions, operating systems, or modified copies of the HTML. Treat exported files and clipboard contents according to your own data-handling requirements.

The badges in this README are hosted by Shields.io. They are documentation assets only; the HTML application does not load them.

## Repository files

```text
.
├── json-inspector-3.0.html
├── README.md
└── README.ru.md
```

## Validation and compatibility

This revision passed JavaScript syntax checks, HTML identifier/script-integrity checks, 693 parser/format/path assertions, and 383 UI-state/translation assertions on a minimal DOM mock. Tests include 300 generated JSON documents, exact numeric tokens, invalid input, duplicate keys, path resolution, limits, and language switching without source changes.

The DOM mock is not a browser. Visual rendering, screen-reader behavior, real downloads, drag-and-drop, and system clipboard access still require manual browser testing. No cross-browser certification or claim of complete security is implied.

## License

MIT `LICENSE`
