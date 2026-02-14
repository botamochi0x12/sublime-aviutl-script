# Contract: package.json (Extension Manifest)

**Feature**: 002-vscode-extension-migration
**Date**: 2026-02-13

This extension is purely declarative — there are no API endpoints. The "contracts" describe the structure of the configuration files that VS Code reads at extension load time.

## package.json

```jsonc
{
  "name": "aviutl-script",
  "displayName": "AviUtl Script",
  "description": "Syntax highlighting and snippets for AviUtl scripting (Lua-based)",
  "version": "0.1.0",
  "engines": {
    "vscode": "^1.80.0"
  },
  "categories": ["Programming Languages"],
  "contributes": {
    "languages": [
      {
        "id": "aviutl-script",
        "aliases": ["AviUtl Script (Lua)", "aviutl-script"],
        "extensions": [".obj", ".anm", ".scn", ".cam", ".aviutl-script"],
        "firstLine": "^-- AviUtl Script --",
        "configuration": "./language-configuration.json"
      }
    ],
    "grammars": [
      {
        "language": "aviutl-script",
        "scopeName": "source.lua.aviutl-script",
        "path": "./syntaxes/aviutl-script.tmLanguage.json"
      }
    ],
    "snippets": [
      {
        "language": "aviutl-script",
        "path": "./snippets/aviutl-script.code-snippets"
      }
    ]
  },
  "devDependencies": {
    "vscode-tmgrammar-test": "^0.1.3"
  },
  "scripts": {
    "test": "vscode-tmgrammar-test -s source.lua.aviutl-script -g syntaxes/aviutl-script.tmLanguage.json -t 'test/**/*.aviutl-script'"
  }
}
```

## Validation rules

- `name`: must be lowercase, no spaces (npm package name rules)
- `engines.vscode`: minimum VS Code version supporting all used contribution points
- `contributes.languages[0].extensions`: must match spec FR-001 (`.obj`, `.anm`, `.scn`, `.cam`, `.aviutl-script`)
- `contributes.languages[0].firstLine`: must match spec FR-002 (`^-- AviUtl Script --`)
- `contributes.grammars[0].scopeName`: must match grammar file's `scopeName` field exactly
- `contributes.grammars[0].path`: must point to an existing file
- `contributes.snippets[0].path`: must point to an existing file
