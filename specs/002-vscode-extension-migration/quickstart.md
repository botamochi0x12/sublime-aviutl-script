# Quickstart: VS Code Extension Migration

**Feature**: 002-vscode-extension-migration
**Date**: 2026-02-13

## Prerequisites

- Node.js (for `npm` — dev dependency management and test runner)
- VS Code (for manual testing via Extension Development Host)

## Setup

```bash
# From repository root
cd vscode-aviutl-script
npm install
```

## Development workflow (TDD)

### 1. Write a failing test

Edit `test/aviutl-script.test.aviutl-script` to add a scope assertion:

```lua
-- SYNTAX TEST "source.lua.aviutl-script"

debug_print()
-- <- support.function.library.aviutl-script
```

### 2. Run tests (Red)

```bash
npm test
```

Expect failure: the grammar pattern doesn't exist yet.

### 3. Add grammar pattern (Green)

Edit `syntaxes/aviutl-script.tmLanguage.json` to add the matching pattern.

### 4. Run tests again

```bash
npm test
```

Expect pass.

### 5. Manual verification

Press **F5** in VS Code (with the `vscode-aviutl-script/` folder open) to launch the Extension Development Host. Open a `.obj` or `.aviutl-script` file and verify highlighting.

## File overview

| File | Purpose |
|------|---------|
| `package.json` | Extension manifest — registers language, grammar, snippets |
| `language-configuration.json` | Comment styles, brackets, indentation rules |
| `syntaxes/aviutl-script.tmLanguage.json` | TextMate grammar (syntax highlighting) |
| `snippets/aviutl-script.code-snippets` | All 55 code snippets |
| `test/aviutl-script.test.aviutl-script` | Grammar scope assertions |

## Key commands

| Command | Purpose |
|---------|---------|
| `npm test` | Run grammar tests (headless) |
| `F5` in VS Code | Launch Extension Development Host |
| `npx vsce package` | Build `.vsix` package for distribution |

## Build sequence

1. `npm install` — install dev dependencies (`vscode-tmgrammar-test`)
2. Write grammar tests → write grammar patterns (TDD cycle)
3. Convert snippets from Sublime XML to VS Code JSON
4. Write language configuration
5. `npm test` — verify all grammar tests pass
6. `F5` — manual verification in Extension Development Host
7. `npx vsce package` — build distributable `.vsix` (optional)
