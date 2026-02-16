# Implementation Plan: Global Function Snippets

**Branch**: `005-global-function-snippets` | **Date**: 2026-02-15 | **Spec**: [spec.md](spec.md)
**Input**: Feature specification from `/specs/005-global-function-snippets/spec.md`

## Summary

Add snippet definitions for the 5 AviUtl global functions (`OR`, `AND`, `XOR`, `SHIFT`, `debug_print`) that are already recognized by the syntax grammar but lack corresponding snippets. Snippets are added in both Sublime Text `.sublime-snippet` format and VS Code `.code-snippets` JSON format, following the exact patterns established by the existing `obj.*` method snippets.

## Technical Context

**Language/Version**: XML (Sublime snippets), JSON (VS Code snippets) — declarative, no runtime code
**Primary Dependencies**: None — pure editor configuration files
**Storage**: N/A
**Testing**: Manual snippet trigger verification in both editors; `vscode-tmgrammar-test` for grammar (no changes to grammar, but confirms no regressions)
**Target Platform**: Sublime Text 3, VS Code 1.80+
**Project Type**: Editor extension package (declarative)
**Performance Goals**: N/A — snippets are statically loaded by the editor
**Constraints**: None — additive change only
**Scale/Scope**: 5 new Sublime snippet files + 5 new entries in VS Code snippet JSON

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

| Principle | Status | Notes |
| --------- | ------ | ----- |
| I. Simplicity (YAGNI) | PASS | Each snippet file solves a concrete, current need (missing autocompletion for known functions). No abstractions introduced. |
| II. Test-First (TDD) | PASS | TDD applies to behavioral changes. Snippets are declarative data — they will be verified by manual trigger tests and by ensuring the VS Code grammar tests still pass. |
| III. Syntax Fidelity | N/A | No grammar/scope changes. Snippets use existing scope `source.lua.aviutl-script`. |
| IV. Backward Compatibility | PASS | Additive only — 5 new files in Sublime `snippet/function/`, 5 new entries in VS Code JSON. No existing files modified except appending to the VS Code snippets JSON. |
| V. Single Source of Truth | PASS | Function names and signatures are sourced from the AviUtl scripting reference. Snippet content matches grammar patterns already defined in both `AviUtl.sublime-syntax` and `aviutl-script.tmLanguage.json`. |
| Keyword Accuracy | PASS | All 5 functions (`OR`, `AND`, `XOR`, `SHIFT`, `debug_print`) already exist in the grammar files, verified against the AviUtl reference. |

No violations. No complexity tracking needed.

## Project Structure

### Documentation (this feature)

```text
specs/005-global-function-snippets/
├── plan.md              # This file
├── research.md          # Phase 0 output
├── quickstart.md        # Phase 1 output
└── tasks.md             # Phase 2 output (created by /speckit.tasks)
```

### Source Code (repository root)

```text
sublime-aviutl-script/
└── snippet/
    └── function/
        ├── OR.sublime-snippet          # NEW
        ├── AND.sublime-snippet         # NEW
        ├── XOR.sublime-snippet         # NEW
        ├── SHIFT.sublime-snippet       # NEW
        └── debug_print.sublime-snippet # NEW

vscode-aviutl-script/
└── snippets/
    └── aviutl-script.code-snippets     # MODIFIED (5 entries appended)
```

**Structure Decision**: Follows existing convention — each Sublime snippet is a separate `.sublime-snippet` file in `snippet/function/`, while VS Code snippets are all in the single `aviutl-script.code-snippets` JSON file. Global functions (not prefixed with `obj.`) get their own snippet files named after the function.

## Snippet Definitions

### Sublime Text Snippets

Each `.sublime-snippet` file follows this template:

```xml
<snippet>
	<scope>source.lua.aviutl-script</scope>
	<description>{Japanese description}</description>
	<content><![CDATA[
{expansion}
]]></content>
	<tabTrigger>{trigger}</tabTrigger>
</snippet>
```

| File | Trigger | Expansion | Description |
| ---- | ------- | --------- | ----------- |
| `OR.sublime-snippet` | `OR` | `OR(${1:a},${2:b})$0` | OR,AND,XORのビット演算をします。 |
| `AND.sublime-snippet` | `AND` | `AND(${1:a},${2:b})$0` | OR,AND,XORのビット演算をします。 |
| `XOR.sublime-snippet` | `XOR` | `XOR(${1:a},${2:b})$0` | OR,AND,XORのビット演算をします。 |
| `SHIFT.sublime-snippet` | `SHIFT` | `SHIFT(${1:a},${2:b})$0` | 算術シフトをします。 |
| `debug_print.sublime-snippet` | `debug_print` | `debug_print(${1:str})$0` | デバック用の表示に使用します。 |

### VS Code Snippet Entries

Appended to `aviutl-script.code-snippets`:

```json
"OR": {
  "prefix": "OR",
  "body": "OR(${1:a},${2:b})$0",
  "description": "OR,AND,XORのビット演算をします。"
},
"AND": {
  "prefix": "AND",
  "body": "AND(${1:a},${2:b})$0",
  "description": "OR,AND,XORのビット演算をします。"
},
"XOR": {
  "prefix": "XOR",
  "body": "XOR(${1:a},${2:b})$0",
  "description": "OR,AND,XORのビット演算をします。"
},
"SHIFT": {
  "prefix": "SHIFT",
  "body": "SHIFT(${1:a},${2:b})$0",
  "description": "算術シフトをします。"
},
"debug_print": {
  "prefix": "debug_print",
  "body": "debug_print(${1:str})$0",
  "description": "デバック用の表示に使用します。"
}
```

## Implementation Sequence

1. Create 5 Sublime `.sublime-snippet` files in `sublime-aviutl-script/snippet/function/`
2. Append 5 entries to `vscode-aviutl-script/snippets/aviutl-script.code-snippets`
3. Run `vscode-tmgrammar-test` to confirm no regressions
4. Manual verification of snippet triggers in both editors
