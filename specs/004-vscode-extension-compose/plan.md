# Implementation Plan: Compose VS Code Extension

**Branch**: `004-vscode-extension-compose` | **Date**: 2026-02-15 | **Spec**: [spec.md](spec.md)
**Input**: Feature specification from `specs/004-vscode-extension-compose/spec.md`

## Summary

Package the existing `vscode-aviutl-script/` directory (grammar, snippets, language config, tests) into a publishable VS Code extension. This involves adding Marketplace metadata to `package.json`, creating README/CHANGELOG/LICENSE files, providing an icon, fixing `.vscodeignore`, and adding `@vscode/vsce` as a dev dependency for packaging. No runtime code is needed — this is a purely declarative extension.

## Technical Context

**Language/Version**: JSON (declarative extension manifest); Node.js >=20.x (tooling only)
**Primary Dependencies**: `@vscode/vsce` (packaging CLI), `vscode-tmgrammar-test` (existing test tool)
**Storage**: N/A (static declarative extension — no runtime state)
**Testing**: `vscode-tmgrammar-test` for grammar assertions; `vsce ls` to verify package contents
**Target Platform**: VS Code ^1.80.0 (all platforms — declarative extension is platform-independent)
**Project Type**: Single project (VS Code extension within monorepo subdirectory)
**Performance Goals**: N/A (declarative extension — no runtime code)
**Constraints**: Package size should be minimal (exclude dev files); Marketplace validation must pass
**Scale/Scope**: ~10 files to create/modify; single-command packaging workflow

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

| Principle | Status | Notes |
|-----------|--------|-------|
| I. Simplicity (YAGNI) | PASS | No abstractions, wrappers, or speculative code. Only files required for packaging/publishing. |
| II. Test-First (TDD) | PASS | Existing grammar tests already pass. New packaging workflow verified by `vsce package` + `vsce ls`. TDD applies to behavioral code; metadata files are declarative and validated by tooling. |
| III. Syntax Fidelity | PASS | No grammar changes. Existing scopes preserved as-is. |
| IV. Backward Compatibility | PASS | No changes to Sublime Text files (`AviUtl.sublime-syntax`, `snippet/`, syntax test). VS Code extension is additive. |
| V. Single Source of Truth for Keywords | PASS | No keyword changes. Grammar file remains the single source for VS Code scopes. |

**Gate result**: All principles pass. No violations require justification.

## Project Structure

### Documentation (this feature)

```text
specs/004-vscode-extension-compose/
├── plan.md              # This file
├── research.md          # Phase 0 output
├── data-model.md        # Phase 1 output
├── quickstart.md        # Phase 1 output
└── tasks.md             # Phase 2 output (/speckit.tasks command)
```

### Source Code (repository root)

```text
vscode-aviutl-script/
├── package.json              # MODIFY: add publisher, repository, icon, keywords, license, vsce:prepublish
├── package-lock.json         # AUTO-UPDATED by npm install
├── .vscodeignore             # MODIFY: fix *.md exclusion, add more patterns
├── README.md                 # CREATE: extension description for Marketplace
├── CHANGELOG.md              # CREATE: version history
├── LICENSE                   # CREATE: MIT license file
├── images/
│   └── icon.png              # CREATE: 256x256 extension icon
├── language-configuration.json   # EXISTING (no changes)
├── syntaxes/
│   └── aviutl-script.tmLanguage.json  # EXISTING (no changes)
├── snippets/
│   └── aviutl-script.code-snippets    # EXISTING (no changes)
├── test/
│   ├── aviutl-script.test.aviutl-script  # EXISTING (no changes)
│   └── grammars/
│       └── lua.tmLanguage.json           # EXISTING (no changes)
└── node_modules/             # EXCLUDED from package
```

**Structure Decision**: Single project within monorepo subdirectory. No new directories needed except `images/` for the icon. All changes are additions or modifications to existing files within `vscode-aviutl-script/`.

## Complexity Tracking

> No violations detected. Table intentionally left empty.
