# Data Model: VS Code Extension Migration

**Feature**: 002-vscode-extension-migration
**Date**: 2026-02-13

This extension is purely declarative — no runtime data structures, databases, or state. The "data model" describes the structure of the static configuration files that VS Code consumes.

## Entity: TextMate Grammar

**File**: `syntaxes/aviutl-script.tmLanguage.json`
**Format**: TextMate grammar JSON (VS Code variant)

### Top-level fields

| Field | Type | Value | Notes |
|-------|------|-------|-------|
| `$schema` | string | `https://raw.githubusercontent.com/martinring/tmlanguage/master/tmlanguage.json` | Optional; enables editor validation |
| `name` | string | `"AviUtl Script (Lua)"` | Display name |
| `scopeName` | string | `"source.lua.aviutl-script"` | Root scope; extends `source.lua` |
| `patterns` | array | Pattern references | Order matters: AviUtl patterns first, then `source.lua` include |
| `repository` | object | Named pattern groups | Contains `section-label`, `aviutl-functions`, `obj-mutable-properties`, `obj-readonly-properties`, `obj-methods` |

### Repository entries

**`section-label`** — Matches `@label_name` at line start
- Pattern: `^\s*(\@)(\S+)(?=\s|-|$)`
- Capture 1 → `punctuation.definition.keyword.aviutl-script keyword.declaration.extends.aviutl-script`
- Capture 2 → `entity.name.label.aviutl-script`

**`aviutl-functions`** — Matches global AviUtl functions
- Pattern: `\b(OR|AND|XOR|RGB|HSV|SHIFT|debug_print)\b`
- Name → `support.function.library.aviutl-script`

**`obj-mutable-properties`** — Matches `obj.{mutable_prop}`
- Pattern: `\b(obj)\b(\\.)(ox|oy|oz|zoom|alpha|aspect|rx|ry|rz|cx|cy|cz)\b`
- Capture 1 → `constant.language.aviutl-script support.constant.builtin.aviutl-script`
- Capture 2 → `punctuation.accessor.dot.aviutl-script`
- Capture 3 → `variable.other.aviutl-script`

**`obj-readonly-properties`** — Matches `obj.{readonly_prop}`
- Pattern: `\b(obj)\b(\\.)(w|h|screen_w|screen_h|index|num|time|totaltime|frame|totalframe|framerate|x|y|z|layer|check0|track0|track1|track2|track3|color|dialog|file|param)\b`
- Capture 1 → `constant.language.aviutl-script support.constant.builtin.aviutl-script`
- Capture 2 → `punctuation.accessor.dot.aviutl-script`
- Capture 3 → `entity.name.constant.aviutl-script`

**`obj-methods`** — Matches `obj.{method}()`
- Pattern: `\b(obj)\b(\\.)((mes|effect|rand|draw|drawpoly|load|setfont|filter|setanchor|interpolation|getaudio|setoption|getoption|getvalue|getinfo|copybuffer|getpixel|putpixel|copypixel|pixeloption|getpixeldata|putpixeldata))\b`
- Capture 1 → `constant.language.aviutl-script support.constant.builtin.aviutl-script`
- Capture 2 → `punctuation.accessor.dot.aviutl-script`
- Capture 3 → `support.function.library.aviutl-script`

### Pattern order (top-level `patterns` array)

1. `#section-label`
2. `#aviutl-functions`
3. `#obj-mutable-properties`
4. `#obj-readonly-properties`
5. `#obj-methods`
6. `source.lua` (include base Lua grammar — must be last)

---

## Entity: Snippet Collection

**File**: `snippets/aviutl-script.code-snippets`
**Format**: VS Code snippet JSON

### Snippet entry structure

Each snippet is a JSON key-value pair:

```
"<display-name>": {
  "prefix": "<tab-trigger>",
  "body": "<template-with-tabstops>",
  "description": "<Japanese description>"
}
```

### Snippet categories (55 total)

| Category | Count | Prefix pattern | Body pattern |
|----------|-------|---------------|-------------|
| Environment | 1 | `!env` | `-- AviUtl Script --` |
| Method | 21 | method name (e.g., `draw`, `mes`) | `obj.<method>(${1:args})$0` |
| Setoption variant | 6 | option name (e.g., `blend`, `culling`) | `obj.setoption("<option>",${1:value})$0` |
| Property | 27 | property name (e.g., `ox`, `time`) | `obj.<property>` |

---

## Entity: Language Configuration

**File**: `language-configuration.json`
**Format**: VS Code language configuration JSON

| Field | Value |
|-------|-------|
| `comments.lineComment` | `"--"` |
| `comments.blockComment` | `["--[[", "]]"]` |
| `brackets` | `["{","}"]`, `["[","]"]`, `["(",")"]` |
| `autoClosingPairs` | `{}`, `[]`, `()`, `""`, `''` (with `notIn` guards) |
| `surroundingPairs` | `{}`, `[]`, `()`, `""`, `''` |
| `indentationRules.increaseIndentPattern` | Lua block openers: `function`, `if.*then`, `else`, `for.*do`, `while.*do`, `repeat` |
| `indentationRules.decreaseIndentPattern` | Lua block closers: `end`, `else`, `elseif`, `until` |

---

## Entity: Extension Manifest

**File**: `package.json`
**Format**: VS Code extension manifest JSON

### Language contribution

| Field | Value |
|-------|-------|
| `id` | `"aviutl-script"` |
| `aliases` | `["AviUtl Script (Lua)", "aviutl-script"]` |
| `extensions` | `[".obj", ".anm", ".scn", ".cam", ".aviutl-script"]` |
| `firstLine` | `"^-- AviUtl Script --"` |
| `configuration` | `"./language-configuration.json"` |

### Grammar contribution

| Field | Value |
|-------|-------|
| `language` | `"aviutl-script"` |
| `scopeName` | `"source.lua.aviutl-script"` |
| `path` | `"./syntaxes/aviutl-script.tmLanguage.json"` |

### Snippet contribution

| Field | Value |
|-------|-------|
| `language` | `"aviutl-script"` |
| `path` | `"./snippets/aviutl-script.code-snippets"` |
