# Data Model: Compose VS Code Extension

**Feature**: [spec.md](spec.md) | **Date**: 2026-02-15

## Overview

This feature has no runtime data model — the extension is purely declarative. This document describes the **artifact model**: the files that compose the extension and their relationships.

## Entity: Extension Manifest (`package.json`)

The central configuration file. All other artifacts are referenced from here.

| Field | Type | Required | Value/Source |
|-------|------|----------|-------------|
| `name` | string | yes | `"aviutl-script"` (existing) |
| `displayName` | string | yes | `"AviUtl Script"` (existing) |
| `description` | string | yes | (existing) |
| `version` | string | yes | `"0.1.0"` (existing) |
| `publisher` | string | yes | TBD by repo owner |
| `engines.vscode` | string | yes | `"^1.80.0"` (existing) |
| `categories` | string[] | yes | `["Programming Languages"]` (existing) |
| `keywords` | string[] | recommended | `["AviUtl", "Lua", "aviutl-script", "video editing", "scripting"]` |
| `icon` | string | recommended | `"images/icon.png"` → references Icon artifact |
| `license` | string | recommended | `"MIT"` → references LICENSE file |
| `repository` | object | recommended | `{ "type": "git", "url": "..." }` |
| `contributes.languages` | object[] | yes | (existing, no changes) |
| `contributes.grammars` | object[] | yes | (existing, no changes) → references Grammar |
| `contributes.snippets` | object[] | yes | (existing, no changes) → references Snippets |
| `scripts.vscode:prepublish` | string | recommended | `"npm test"` |
| `scripts.package` | string | convenience | `"vsce package"` |

## Entity: Extension Package (`.vsix`)

The distributable artifact. Built by `vsce package` from the manifest and included files.

**Includes** (after `.vscodeignore` filtering):
- `package.json`
- `README.md`
- `CHANGELOG.md`
- `LICENSE`
- `images/icon.png`
- `language-configuration.json`
- `syntaxes/aviutl-script.tmLanguage.json`
- `snippets/aviutl-script.code-snippets`

**Excludes** (via `.vscodeignore`):
- `test/**`
- `node_modules/**`
- `*.vsix`
- `package-lock.json`

## Relationships

```text
package.json
├── references → images/icon.png         (icon field)
├── references → language-configuration.json  (contributes.languages.configuration)
├── references → syntaxes/*.tmLanguage.json   (contributes.grammars.path)
├── references → snippets/*.code-snippets     (contributes.snippets.path)
├── implies    → LICENSE                  (license field)
├── implies    → README.md               (Marketplace display)
└── implies    → CHANGELOG.md            (Marketplace display)

.vscodeignore
└── controls   → .vsix contents          (inclusion/exclusion filter)
```

## State Transitions

N/A — no runtime state. The extension lifecycle is:
1. **Source** → `vsce package` → **`.vsix`**
2. **`.vsix`** → `vsce publish` → **Marketplace listing**
3. **Marketplace listing** → user install → **Installed extension**
