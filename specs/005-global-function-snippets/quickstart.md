# Quickstart: Global Function Snippets

## What's Being Added

5 snippet definitions for AviUtl global functions: `OR`, `AND`, `XOR`, `SHIFT`, `debug_print`.

## Files to Create

### Sublime Text (5 new files)

Create each file in `sublime-aviutl-script/snippet/function/`:

- `OR.sublime-snippet` — trigger: `OR`, expands to `OR(a,b)`
- `AND.sublime-snippet` — trigger: `AND`, expands to `AND(a,b)`
- `XOR.sublime-snippet` — trigger: `XOR`, expands to `XOR(a,b)`
- `SHIFT.sublime-snippet` — trigger: `SHIFT`, expands to `SHIFT(a,b)`
- `debug_print.sublime-snippet` — trigger: `debug_print`, expands to `debug_print(str)`

### VS Code (1 modified file)

Append 5 entries to `vscode-aviutl-script/snippets/aviutl-script.code-snippets` (before the closing `}`).

## Verification

1. Run existing grammar tests: `cd vscode-aviutl-script && npm test`
2. In Sublime Text 3: open a `.obj` file, type `OR` + Tab → should expand
3. In VS Code: open a `.obj` file, type `OR` → autocomplete should offer the snippet
