# JSON Inspector 4.1

![Version](https://img.shields.io/badge/version-4.1-548af7?style=flat-square)
![HTML](https://img.shields.io/badge/HTML-single_file-e34f26?style=flat-square)
![JavaScript](https://img.shields.io/badge/JavaScript-vanilla-f7df1e?style=flat-square)
![Offline](https://img.shields.io/badge/offline-ready-6aab73?style=flat-square)
![Languages](https://img.shields.io/badge/interface-EN%20%7C%20RU-8b9ebd?style=flat-square)
![Dependencies](https://img.shields.io/badge/runtime_dependencies-0-6aab73?style=flat-square)

[English](README.md) · [Русский](README.ru.md)

A compact, local-first JSON inspector in a single HTML file. Explore trees and tables, inspect values, edit source, and compare JSON documents without a server, installation, or external libraries.

Version 4.1 builds on the compact 3.0 interface, adding background parsing and comparison, configurable search, Unicode warnings, and exact numeric comparison.

The dark palette is inspired by developer tools such as IntelliJ IDEA. This is an independent project.

## Quick start

1. Save `JSON-Inspector-4.1.html` to your computer.
2. Open it in a modern browser.
3. Click Open, drop a JSON file into the window, or paste JSON into JSON Source and click Apply.
4. Use Sample to explore the built-in document.
5. To compare documents, apply the primary JSON, open JSON Diff, and load a second file.

There is no build step. Node.js, Python, a package manager, and a local web server are not required to use the application.

The application is designed to run from a local `file://` page. Background operations require Web Workers created from Blob URLs. If browser policy blocks Worker startup, the application displays an error instead of falling back to synchronous parsing. Local-file behavior still needs verification in your target browser.

## Features

| Area | Capabilities |
| --- | --- |
| Tree | Expand/collapse branches, select nodes, paginated rendering |
| Tables | Records for arrays of objects; Paths for leaf values and empty containers |
| Search | Keys, values, paths, one optional node-type filter, exact matching, case sensitivity |
| Navigation | Next/previous search match, simple path resolution, parent navigation |
| Inspector | Type, depth, direct-child count, canonical path, original JSON fragment |
| Source | Editing, line numbers, highlighting, synchronized preview, formatting, minification |
| JSON Diff | Added, Removed, Modified, Unchanged; status filter, search, pagination |
| Numbers | Original numeric-token preservation; exact value or token comparison |
| Unicode | Warnings for unpaired UTF-16 surrogates without silently changing source |
| Background work | Parsing, formatting, and comparison in a Web Worker, with cancellation |
| Statistics | UTF-8 size, nodes, properties, objects, arrays, strings, numbers, maximum depth |
| Export | Copy source, copy path/subtree, download JSON |
| Interface | Compact dark layout with English and Russian controls and diagnostics |

## Interface language

Click EN to switch to English or RU to switch to Russian. The button names the language you can switch to.

The initial language follows the browser: Russian for a Russian language setting, English otherwise. Switching languages does not translate or modify JSON keys, values, or file names. No language preference or document is written to browser storage.

## Tree, tables, and inspector

- Click a tree value to open its inspector.
- Use ↗ to open a container, or a value's parent, in the table.
- Click table values to inspect individual nodes; use ↗ beside containers to drill into them.
- Records combines object properties into columns. Missing properties appear as `—`; JSON `null` remains a value.
- Paths lists leaf values and empty containers below the selected table root.
- Parent navigates to the selected node's parent.
- Copy Path copies the canonical path. Copy Subtree copies the complete original JSON fragment, including its original numeric tokens.
- Long previews are truncated; copying still uses the complete value.

Internal node selection and parent relationships use numeric indices, not display paths.

## Search

Enable any combination of Keys, Values, and Paths. Enabled areas are combined with OR: a match in any area is sufficient. The optional type filter then restricts results to one node type.

Exact match compares the query with an individual search area. Case sensitive preserves case during matching. Queries are literal, not regular expressions; case-insensitive matching is not full Unicode case folding.

String values are searched in decoded form; numeric values use their original tokens. Booleans and null use their JSON text. For objects and arrays, value search uses the type name, not the serialized subtree.

Use ↑ / ↓ to move between matches. Search runs in the main thread after a short input delay, not in the Worker.

## Paths

Supported forms include:

```text
$
$.users[0].name
$["users"][0]["name"]
$["key.with.dot"]["hello world"]
```

Canonical paths use JSON-quoted object keys. Dot syntax accepts identifier-like names; use double-quoted bracket syntax for punctuation, spaces, Unicode keys, and unusual names.

This is not a full JSONPath engine. Wildcards, filters, recursive descent, slices, and executable expressions are not supported. Duplicate decoded keys are rejected, making canonical paths unambiguous within an applied document.

## JSON Diff

Diff compares the last successfully applied primary document with a separately loaded comparison document. Apply source edits before comparing them.

| Status | Meaning |
| --- | --- |
| Added | Exists only in the comparison document |
| Removed | Exists only in the primary document |
| Modified | Type, value, or descendant content differs |
| Unchanged | Equal under the selected comparison rules |

Changes propagate to ancestor containers. Summary counts include containers and refer to nodes, not independent editing operations.

### Comparison rules

- Object properties are matched by decoded key; property order is ignored.
- Arrays are matched by index. Moves and shifted elements are not recognized as separate operations.
- Strings are compared by decoded value, not escape spelling.
- Formatting whitespace does not affect semantic equality.
- Duplicate decoded keys are rejected in both documents.

The comparison file remains loaded when the primary document is reapplied. Reopen the Diff view to rebuild the comparison.

Filter by status, use search, and browse pages. Click a displayed value to copy its original JSON fragment. Use ↗ beside a primary-document value to reveal it in the tree.

### Number modes

| Mode | Behavior |
| --- | --- |
| Exact numeric value | Normalizes decimal coefficients and exponents without JavaScript Number conversion |
| Number lexeme | Compares original tokens, including notation and trailing zeros |

Equivalent decimal and exponential representations compare equal in numeric-value mode. A separate option determines whether negative and positive zero are distinct in that mode. Lexeme mode always compares original spelling.

## Data integrity

### Numeric-token preservation

The parser retains numbers as text. Formatting, minification, and copying preserve tokens such as:

```json
{
  "largeId": 9007199254740993,
  "precise": 0.123456789012345678901,
  "negativeZero": -0,
  "largeExponent": 1e999
}
```

Exact comparison uses string normalization and BigInt exponents, without expanding large exponents into decimal strings. Downstream tools may still be unable to represent these numbers accurately.

### Unicode and decoding

Unpaired UTF-16 surrogates generate warnings; source data is not silently repaired or normalized. Warning details identify the beginning of the affected string token using zero-based UTF-16 offsets, not the exact location of every surrogate.

Malformed escapes and unescaped control characters are parsing errors. File text is decoded as UTF-8; malformed byte sequences are not separately validated. A leading BOM is removed when opening either file.

### Duplicate keys and invalid input

Duplicate decoded keys are rejected rather than overwritten: `"a"` and `"\u0061"` count as the same key. This is an intentional application policy.

The parser accepts JSON, not JSON5 or JSON Lines. Comments, trailing commas, NaN, and Infinity are rejected. Diagnostics include line and column where available.

A parse error preserves the last applied tree, tables, and statistics. The editor may contain a newer invalid draft.

### Applying is not saving

Apply updates the views; it does not save a file. Replacement and page-close warnings help prevent accidental loss, subject to browser dialog behavior.

Download requests an export but cannot confirm that the browser completed it. Verify that the file was saved. The original input file is not edited in place.

## Background processing

Parsing, formatting/minification, and structural comparison run in a Web Worker. Only one computational request is active at a time; a new request cancels the previous one. Cancel stops the active operation.

Search, model transfer, selector preparation, highlighting, and DOM rendering still involve main-thread work. Background processing does not guarantee a completely non-blocking interface.

## Keyboard controls

| Key | Action |
| --- | --- |
| Cmd + Enter / Ctrl + Enter in editor | Apply JSON |
| Tab in editor | Insert two spaces |
| Enter in path field | Go to path |
| Esc | Close inspector |
| Left / Right on a view tab | Switch view tabs |
| Home / End on a view tab | Select first / last view tab |

Clipboard access depends on browser permissions. If copying fails, select the source and use your normal copy shortcut.

## Limits

| Resource | Limit |
| --- | ---: |
| Input / formatted output | 5 MiB |
| Nodes per document | 100,000 |
| Nesting depth | 150; root depth is 0 |
| Combined canonical-path length | 8,388,608 UTF-16 code units |
| Tree page | 250 nodes |
| Table / Diff page | 100 rows |
| Record-table property columns | 40, plus index |
| Highlighted source | 200,000 UTF-16 code units |
| Inspector preview | 100,000 UTF-16 code units; copying uses the full value |

When record columns exceed the limit, use Paths to access omitted values. Highlighting and line numbers turn off for large source documents; editing remains available.

Limits reduce resource use but do not guarantee instant responses or a fixed memory ceiling. Performance depends on document shape, string and key lengths, device, and browser.

## Privacy and security

- Processing runs locally; the application makes no network requests and has no runtime dependencies.
- Documents are not persisted in localStorage, IndexedDB, or cookies.
- User-controlled values are inserted with text DOM operations rather than HTML interpolation.
- Object lookup uses Map. Paths are not evaluated as code.
- CSP blocks network connections, embedded objects, and frames. Inline scripts/styles and Blob Workers are allowed to keep the application self-contained.
- Clipboard and download actions are initiated by the user.

Local processing does not protect against compromised browsers, extensions, operating systems, or modified HTML copies. Handle clipboard contents and exported files according to your requirements.

README badges are hosted by Shields.io. They are documentation assets; the HTML application does not load them.

## Repository files

Suggested layout:

```text
.
├── JSON-Inspector-4.1.html
├── README.md
├── README.ru.md
└── LICENSE
```

Only the HTML file is required to run the application. No separate test script or development environment is required by this README.

## Validation and compatibility

During development, the computational core passed 40 automated checks covering numeric tokens, negative zero, exponents, paths, duplicate keys, Unicode, invalid input, exact comparison, ancestor changes, array comparison, and a document containing 10,000 values.

JavaScript syntax and static DOM-identifier checks also passed. These are development checks, not a complete browser test or a guarantee that later edits remain correct. The test implementation used a separate copy of core functions rather than automatically extracting the current HTML code.

A browser end-to-end run was not performed for this release. Visual rendering, Worker startup under the actual CSP, accessibility, focus, downloads, drag-and-drop, and system clipboard access still require manual browser testing.

No cross-browser certification or claim of complete security is implied.

## License

MIT. See LICENSE.
