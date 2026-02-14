# Contract: TextMate Grammar Schema

**Feature**: 002-vscode-extension-migration
**Date**: 2026-02-13

## aviutl-script.tmLanguage.json

```jsonc
{
  "$schema": "https://raw.githubusercontent.com/martinring/tmlanguage/master/tmlanguage.json",
  "name": "AviUtl Script (Lua)",
  "scopeName": "source.lua.aviutl-script",
  "patterns": [
    { "include": "#section-label" },
    { "include": "#aviutl-functions" },
    { "include": "#obj-mutable-properties" },
    { "include": "#obj-readonly-properties" },
    { "include": "#obj-methods" },
    { "include": "source.lua" }
  ],
  "repository": {
    "section-label": {
      // Pattern and captures for @label_name
    },
    "aviutl-functions": {
      // Pattern for OR, AND, XOR, RGB, HSV, SHIFT, debug_print
    },
    "obj-mutable-properties": {
      // Pattern for obj.ox, obj.oy, etc.
    },
    "obj-readonly-properties": {
      // Pattern for obj.w, obj.time, etc.
    },
    "obj-methods": {
      // Pattern for obj.draw(), obj.mes(), etc.
    }
  }
}
```

## Scope mapping contract

The following scope assignments MUST be preserved from the Sublime grammar (Constitution Principle III — Syntax Fidelity):

| Token | Scope | Example |
|-------|-------|---------|
| `obj` (before `.property`) | `constant.language.aviutl-script support.constant.builtin.aviutl-script` | `obj.ox` → `obj` |
| `.` (dot accessor) | `punctuation.accessor.dot.aviutl-script` | `obj.ox` → `.` |
| Mutable property | `variable.other.aviutl-script` | `obj.ox` → `ox` |
| Read-only property | `entity.name.constant.aviutl-script` | `obj.time` → `time` |
| Method name | `support.function.library.aviutl-script` | `obj.draw` → `draw` |
| Global function | `support.function.library.aviutl-script` | `debug_print` |
| Section `@` | `punctuation.definition.keyword.aviutl-script keyword.declaration.extends.aviutl-script` | `@new` → `@` |
| Section label name | `entity.name.label.aviutl-script` | `@new` → `new` |

## Pattern order constraint

AviUtl-specific patterns MUST appear before the `source.lua` include in the top-level `patterns` array. This ensures AviUtl patterns take priority over base Lua patterns when both could match (spec FR-011).
