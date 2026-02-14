# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Sublime Text 3 syntax highlighting and snippet package for **AviUtl scripting** — a Lua-based scripting language used by the AviUtl video editor (Japanese). The package extends Sublime's built-in Lua syntax with AviUtl-specific keywords, object properties, methods, and section labels.

## Architecture

**`AviUtl.sublime-syntax`** — The core syntax definition (YAML, Sublime Text `.sublime-syntax` format). Extends `source.lua` via context includes. Defines:
- Section labels: `@label_name`
- AviUtl global functions: `OR`, `AND`, `XOR`, `RGB`, `HSV`, `SHIFT`, `debug_print`
- `obj.*` property/method patterns with three capture groups (`obj`, `.`, `member`), scoped differently:
  - Mutable properties → `variable.other.aviutl-script` (ox, oy, oz, zoom, alpha, aspect, rx, ry, rz, cx, cy, cz)
  - Read-only constants → `entity.name.constant.aviutl-script` (w, h, screen_w, time, frame, etc.)
  - Methods → `support.function.library.aviutl-script` (mes, draw, load, effect, etc.)

**`snippet/`** — 50 Sublime snippets (XML `.sublime-snippet` format), organized as:
- `snippet/AviUtl.sublime-snippet` — Environment template (`!env` trigger)
- `snippet/function/` — 21 method snippets (draw, load, mes, etc.)
- `snippet/function/setoption/` — 6 setoption variant snippets
- `snippet/variable/` — 27 property snippets (obj.ox, obj.x, etc.)

**`syntax-test_aviutl-script.aviutl-script`** — Sublime syntax test file with scope assertions.

## Testing

Run syntax tests in Sublime Text 3 via **Tools > Build** with the test file open, or use the command palette: **Build With: Syntax Tests**. The test file uses `-- SYNTAX TEST` header and `-- ^ scope.name` / `-- <- scope.name` assertion comments.

## Key References

- AviUtl scripting keyword reference: https://ch.nicovideo.jp/usunoro/blomaga/ar915424
- Sublime syntax docs: http://www.sublimetext.com/docs/3/syntax.html
- The syntax inherits from Sublime's built-in Lua package (`Packages/Lua/Lua.sublime-syntax`)

## File Extensions

`.obj`, `.anm`, `.scn`, `.cam`, `.aviutl-script` — all recognized as AviUtl Script (Lua). First-line match: `-- AviUtl Script --`.

## Active Technologies
- JSON (TextMate grammar, VS Code snippets, language config); Node.js for tooling/testing + VS Code Extension API (declarative — no runtime code needed); `vscode-tmgrammar-test` (dev dependency for grammar testing) (002-vscode-extension-migration)
- N/A (static declarative extension — no runtime state) (002-vscode-extension-migration)

## Recent Changes
- 002-vscode-extension-migration: Added JSON (TextMate grammar, VS Code snippets, language config); Node.js for tooling/testing + VS Code Extension API (declarative — no runtime code needed); `vscode-tmgrammar-test` (dev dependency for grammar testing)
