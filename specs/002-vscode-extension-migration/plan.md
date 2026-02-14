# Implementation Plan: Migrate AviUtl Syntax Highlighting to VS Code Extension

**Branch**: `002-vscode-extension-migration` | **Date**: 2026-02-13 | **Spec**: [spec.md](spec.md)
**Input**: Feature specification from `specs/002-vscode-extension-migration/spec.md`

## Summary

Create a VS Code extension that provides equivalent syntax highlighting and code snippets for AviUtl scripting (Lua-based), converting the existing Sublime Text 3 package into VS Code format. The extension converts the Sublime `.sublime-syntax` (context-based) grammar to a TextMate `.tmLanguage.json` (pattern-based) grammar, converts 55 XML `.sublime-snippet` files into a single VS Code `.code-snippets` JSON file, and adds a `language-configuration.json` for Lua editing features. The existing Sublime package remains untouched (Constitution Principle IV).

## Technical Context

**Language/Version**: JSON (TextMate grammar, VS Code snippets, language config); Node.js for tooling/testing
**Primary Dependencies**: VS Code Extension API (declarative — no runtime code needed); `vscode-tmgrammar-test` (dev dependency for grammar testing)
**Storage**: N/A (static declarative extension — no runtime state)
**Testing**: `vscode-tmgrammar-test` for grammar scope assertions; manual verification in VS Code Extension Development Host
**Target Platform**: VS Code ^1.80.0 (cross-platform: Windows, macOS, Linux)
**Project Type**: Single project (VS Code extension subdirectory within existing repo)
**Performance Goals**: N/A (declarative grammar — performance is handled by VS Code's TextMate engine)
**Constraints**: No runtime code (pure declarative extension); must not modify existing Sublime files
**Scale/Scope**: 1 grammar file, 1 snippets file, 1 language config, 1 package.json; 55 snippets to convert

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

| Principle | Status | Notes |
|-----------|--------|-------|
| **I. Simplicity (YAGNI)** | PASS | Pure declarative extension — no runtime code, no abstractions, no framework. Minimal file count: 4 core files. |
| **II. Test-First (TDD)** | PASS | Grammar scope assertions will be written first using `vscode-tmgrammar-test`, mirroring the existing Sublime syntax test file. Red-green-refactor applies to each grammar pattern. |
| **III. Syntax Fidelity** | PASS | Scope assignments in `.tmLanguage.json` will exactly replicate the Sublime syntax scopes: `variable.other.aviutl-script`, `entity.name.constant.aviutl-script`, `support.function.library.aviutl-script`, `entity.name.label.aviutl-script`, `support.function.aviutl-script`. |
| **IV. Backward Compatibility** | PASS | The VS Code extension lives in a new `vscode-aviutl-script/` directory. No existing Sublime files (`AviUtl.sublime-syntax`, `snippet/`, syntax test) are modified. |
| **V. Single Source of Truth** | REVIEW | The Sublime syntax defines the canonical keyword list today. The VS Code grammar will duplicate these keywords. A shared keyword source is desirable but may violate Principle I if it requires build tooling for a 4-file project. See research.md for decision. |

## Project Structure

### Documentation (this feature)

```text
specs/002-vscode-extension-migration/
├── plan.md              # This file
├── research.md          # Phase 0 output
├── data-model.md        # Phase 1 output
├── quickstart.md        # Phase 1 output
├── contracts/           # Phase 1 output
└── tasks.md             # Phase 2 output (/speckit.tasks command)
```

### Source Code (repository root)

```text
vscode-aviutl-script/
├── package.json                        # Extension manifest (language, grammar, snippets)
├── language-configuration.json         # Comments, brackets, indentation rules
├── syntaxes/
│   └── aviutl-script.tmLanguage.json   # TextMate grammar (converted from .sublime-syntax)
├── snippets/
│   └── aviutl-script.code-snippets     # All 55 snippets (converted from .sublime-snippet XML)
├── test/
│   └── aviutl-script.test.lua          # Grammar scope assertions (vscode-tmgrammar-test format)
├── .vscodeignore                       # Files excluded from VSIX package
└── package-lock.json                   # npm lockfile (dev dependencies only)
```

**Structure Decision**: Single project in a new `vscode-aviutl-script/` subdirectory at the repository root. This is the simplest structure for a declarative VS Code extension and keeps it cleanly separated from the existing Sublime package. No `src/` directory is needed because there is no runtime code — only JSON configuration files.

## Constitution Check — Post-Design Re-evaluation

| Principle | Pre-design | Post-design | Notes |
|-----------|-----------|-------------|-------|
| **I. Simplicity** | PASS | PASS | 4 core files, no runtime code, no abstractions |
| **II. Test-First** | PASS | PASS | TDD workflow documented in quickstart.md; `vscode-tmgrammar-test` enables Red-Green-Refactor |
| **III. Syntax Fidelity** | PASS | PASS | Scope contract in `contracts/grammar-schema.md` maps every token to exact scopes |
| **IV. Backward Compat** | PASS | PASS | All new files in `vscode-aviutl-script/`; no Sublime files modified |
| **V. Single Source** | REVIEW | PASS | Duplication accepted per research R-001; Principle I takes precedence over build tooling |

## Complexity Tracking

> No violations. All principles pass. The Principle V keyword duplication is a documented trade-off (research.md R-001) — Principle I (Simplicity) justifies manual duplication over build tooling for a stable, small keyword set.
