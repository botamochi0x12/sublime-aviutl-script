# Changelog

All notable changes to the "AviUtl Script" extension will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.1.0] - 2026-02-15

### Added

- Syntax highlighting for AviUtl scripting (Lua-based) with TextMate grammar
  - `obj` mutable properties, read-only constants, and methods
  - Section labels (`@label_name`)
  - AviUtl global functions (`OR`, `AND`, `XOR`, `RGB`, `HSV`, `SHIFT`, `debug_print`)
- 55 code snippets for AviUtl scripting
  - Environment template (`!env`)
  - 21 method snippets (`draw`, `load`, `mes`, `effect`, etc.)
  - 6 `setoption` variant snippets
  - 27 property snippets (`obj.ox`, `obj.x`, etc.)
- Language configuration (bracket matching, comment toggling, auto-closing pairs)
- Support for file extensions: `.obj`, `.anm`, `.scn`, `.cam`, `.aviutl-script`
- First-line detection for `-- AviUtl Script --`
