# Research: VS Code Extension Migration

**Feature**: 002-vscode-extension-migration
**Date**: 2026-02-13

## R-001: Single Source of Truth for Keywords (Constitution Principle V)

**Context**: The constitution requires a single authoritative keyword list. Currently, keywords live in `AviUtl.sublime-syntax`. The VS Code grammar (`aviutl-script.tmLanguage.json`) will contain the same keywords in a different format.

**Decision**: Accept manual duplication — both grammars maintain their own keyword lists.

**Rationale**:
- A shared keyword source would require build tooling (e.g., a script that generates both `.sublime-syntax` and `.tmLanguage.json` from a shared YAML/JSON source). This violates Principle I (Simplicity/YAGNI) — adding a build step, a new dependency, and a new abstraction for a 4-file project.
- The keyword list is small and stable (7 global functions, 12 mutable properties, ~20 read-only constants, ~22 methods). Changes are rare — AviUtl's scripting API hasn't changed in years.
- Divergence risk is low and can be mitigated by a simple checklist item in PR reviews: "Keywords match across both grammars."
- If divergence becomes a real problem in the future, build tooling can be added then (YAGNI).

**Alternatives considered**:
1. **Shared keyword JSON + code generator** — Rejected: adds build complexity for a stable, small keyword set. Violates Principle I.
2. **Injection grammar instead of standalone** — Rejected: injection grammars cannot define a new language ID or register file extensions. We need a standalone grammar for `.obj`, `.anm`, etc.
3. **Single grammar file shared between editors** — Not feasible: Sublime uses YAML `.sublime-syntax` (context-based), VS Code uses JSON `.tmLanguage.json` (pattern-based). Fundamentally different formats.

---

## R-002: Sublime Context-Based Grammar to TextMate Pattern-Based Conversion

**Context**: Sublime's `.sublime-syntax` uses a stack-based context system (`contexts`, `push`, `pop`, `include`). TextMate/VS Code uses a flat pattern-matching system with `repository` and `include` references.

**Decision**: Direct pattern conversion with `repository` sections.

**Rationale**:
- The AviUtl grammar is simple — it only uses `include` and `match` (no `push`/`pop`/`set`), making conversion straightforward.
- Each Sublime context maps to a TextMate `repository` entry.
- The `include: scope:source.lua#main` becomes `{ "include": "source.lua" }` in TextMate.
- The `include: scope:source.lua#prototype` is implicit in TextMate — VS Code's engine handles prototype-level patterns (like comments) automatically when inheriting from `source.lua`.

**Conversion mapping**:

| Sublime `.sublime-syntax` | TextMate `.tmLanguage.json` |
|---|---|
| `scope: source.lua.aviutl-script` | `"scopeName": "source.lua.aviutl-script"` |
| `contexts: main:` | `"patterns": [...]` (top-level) |
| `- include: aviutl-script-extension` | `{ "include": "#aviutl-script-extension" }` |
| `- include: scope:source.lua#main` | `{ "include": "source.lua" }` |
| `- match: '\b(OR\|...)\b'` | `"match": "\\b(OR\|...)\\b"` |
| `scope: support.function.library.aviutl-script` | `"name": "support.function.library.aviutl-script"` |
| `captures: 1: ...` | `"captures": { "1": { "name": "..." } }` |
| `- meta_scope: meta.block.aviutl-script` | Omitted (not replicable in TextMate; not used for theming) |

---

## R-003: `\b` Word Boundary Around Dot Accessor

**Context**: The existing Sublime syntax uses `\b(obj)\b(\.)\b(property)\b` for matching `obj.property`. The `\b` between `obj` and `.` and between `.` and `property` may not behave as expected because `.` is not a word character — `\b` matches at a word/non-word boundary, so `obj\b` matches at the boundary between `j` (word) and `.` (non-word), and `\b(property)` matches at the boundary between `.` (non-word) and the first letter (word). This works correctly.

**Decision**: Preserve the existing `\b` pattern in the TextMate grammar. The pattern works correctly in both engines.

**Rationale**: Both Sublime's regex engine (Oniguruma) and VS Code's (also Oniguruma via vscode-textmate) handle `\b` identically. The existing test file validates this behavior.

**Note on test discrepancy**: Line 84 of `syntax-test_aviutl-script.aviutl-script` asserts `variable.other.lua` for `obj.ox`, but the grammar intends `variable.other.aviutl-script`. The test documents **broken behavior** — the mutable property regex does not actually match in Sublime, likely due to prototype context ordering consuming `obj.ox` as a Lua variable access before the AviUtl pattern runs. The read-only (`obj.x`) and method (`obj.mes`) patterns with identical `\b` wrapping DO match correctly, suggesting the issue is context-specific rather than regex-specific. The VS Code TextMate grammar should fix this: placing AviUtl patterns before the `source.lua` include in the `patterns` array ensures explicit priority.

---

## R-004: Snippet Conversion (Sublime XML to VS Code JSON)

**Context**: 55 Sublime snippets in XML `.sublime-snippet` format need conversion to a single VS Code `.code-snippets` JSON file.

**Decision**: Manual conversion with systematic approach.

**Rationale**:
- Snippet format is simple: `tabTrigger` → `prefix`, `content` → `body`, `description` → `description`, `scope` → omitted (file-level scoping in VS Code).
- Tab stop syntax is nearly identical: `$1`, `${1:placeholder}`, `$0` work the same.
- Key escaping differences: double quotes in `body` must be escaped as `\"` in JSON; `\}` in Sublime becomes `\\}` if needed.
- The `scope` field in each `.sublime-snippet` (`source.lua.aviutl-script`) is handled at the file level in VS Code — the `.code-snippets` file is registered to the `aviutl-script` language in `package.json`.

**Snippet count verification**:
- 1 environment template (`!env`)
- 21 function snippets (in `snippet/function/`)
- 6 setoption variant snippets (in `snippet/function/setoption/`)
- 27 variable snippets (in `snippet/variable/`)
- **Total: 55** (matches spec FR-009)

---

## R-005: Language Configuration for Lua Editing

**Decision**: Provide a `language-configuration.json` with Lua-standard settings.

**Details**:
- Line comment: `--`
- Block comment: `--[[` ... `]]`
- Brackets: `()`, `{}`, `[]`
- Auto-closing pairs: `()`, `{}`, `[]`, `""`, `''`, `--[[ ]]` (with `notIn` guards for strings/comments)
- Surrounding pairs: same as auto-closing
- Indentation rules: increase after `function`, `if.*then`, `else`, `elseif.*then`, `for.*do`, `while.*do`, `repeat`; decrease before `end`, `else`, `elseif`, `until`
- Folding: `--region`/`--endregion` markers (optional, standard Lua convention)

---

## R-006: Grammar Testing Strategy (TDD)

**Decision**: Use `vscode-tmgrammar-test` npm package for automated grammar testing.

**Rationale**:
- Syntax is nearly identical to Sublime's syntax test format (`-- ^ scope.name`, `-- <- scope.name`).
- Can be run headlessly via `npm test` — no VS Code instance needed.
- Supports both snapshot and assertion modes.
- Test file can be derived from the existing `syntax-test_aviutl-script.aviutl-script` with minor adjustments.

**Test approach (TDD per Constitution Principle II)**:
1. Write test assertions for each grammar pattern (section labels, global functions, obj.property, obj.method) BEFORE writing the grammar.
2. Run tests — they fail (Red).
3. Add grammar patterns one by one to make tests pass (Green).
4. Refactor grammar if needed (Refactor).

**Test file header format**:
```lua
-- SYNTAX TEST "source.lua.aviutl-script" "aviutl-script"
```

---

## R-007: VS Code Extension Packaging and Distribution

**Decision**: Standard `vsce` packaging. No marketplace publishing in initial scope.

**Rationale**:
- The extension can be installed locally via `.vsix` file or by copying to the VS Code extensions directory.
- Marketplace publishing can be added later if there is demand.
- `package.json` should include all required fields for future marketplace publishing (publisher, repository, license, icon, etc.) but these are not blockers for the initial implementation.

**Minimum `package.json` fields**:
- `name`, `displayName`, `description`, `version`, `engines`, `categories`
- `contributes.languages`, `contributes.grammars`, `contributes.snippets`

---

## R-008: `firstLine` Detection in VS Code

**Decision**: Use `"firstLine": "^-- AviUtl Script --"` in `package.json` language contribution.

**Rationale**: Direct equivalent of Sublime's `first_line_match: '-- AviUtl Script --'`. VS Code supports regex-based first-line matching in the `languages` contribution point.
