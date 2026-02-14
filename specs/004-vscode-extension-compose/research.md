# Research: Compose VS Code Extension

**Feature**: [spec.md](spec.md) | **Date**: 2026-02-15

## R1: VS Code Extension Packaging Tool

**Decision**: Use `@vscode/vsce` (the official VS Code Extension Manager CLI)

**Rationale**: It is the only officially supported tool for creating `.vsix` packages and publishing to the VS Code Marketplace. It handles validation, packaging, and publishing in one tool.

**Alternatives considered**:
- Manual ZIP creation: Not viable — `.vsix` format requires specific internal structure (`extension.vsixmanifest`, content types XML) that `vsce` generates automatically
- `ovsx` (Open VSX): Only needed for Open VSX Registry publishing, not the primary VS Code Marketplace

**Key findings**:
- Package name: `@vscode/vsce` (formerly `vsce`)
- Requires Node.js >=20.x
- Install as devDependency: `npm install --save-dev @vscode/vsce`
- Package command: `npx vsce package` (creates `.vsix` in working directory)
- Verify contents: `npx vsce ls` (lists files included in package)
- Pre-publish hook: `"vscode:prepublish"` script in package.json runs before packaging

## R2: Required package.json Fields for Marketplace

**Decision**: Add the following fields to the existing `package.json`

**Currently present** (no changes needed):
- `name`: "aviutl-script"
- `displayName`: "AviUtl Script"
- `description`: present
- `version`: "0.1.0"
- `engines.vscode`: "^1.80.0"
- `categories`: ["Programming Languages"]

**Must add**:
- `publisher`: Publisher ID (string) — required for Marketplace publishing. Value to be set by the repository owner when a publisher account is created.
- `repository`: `{ "type": "git", "url": "https://github.com/botamochi0x12/sublime-aviutl-script" }` — enables GitHub link on Marketplace page and resolves relative image paths in README
- `icon`: `"images/icon.png"` — path to 256x256 PNG icon
- `license`: `"MIT"` — SPDX identifier matching the LICENSE file
- `keywords`: `["AviUtl", "Lua", "aviutl-script", "video editing", "scripting"]` — max 5 most relevant terms for search
- `homepage`: `"https://github.com/botamochi0x12/sublime-aviutl-script"` — optional but recommended

**Must add to scripts**:
- `"vscode:prepublish": "npm test"` — runs grammar tests before every package/publish

**Rationale**: These fields are documented in the [VS Code Extension Manifest reference](https://code.visualstudio.com/api/references/extension-manifest) as required or strongly recommended for Marketplace listing.

## R3: .vscodeignore Best Practices

**Decision**: Rewrite `.vscodeignore` to properly exclude dev files while keeping README.md and CHANGELOG.md

**Current content** (problematic):
```
test/
node_modules/
.vscode/
*.md
```

**Problem**: `*.md` excludes README.md and CHANGELOG.md, which are displayed on the Marketplace detail page.

**Corrected content**:
```
# Development files
test/**
node_modules/**
.vscode/**

# Build artifacts
*.vsix

# Source control and CI
.git/**
.github/**
.gitignore

# Tooling config
package-lock.json
```

**Rationale**: README.md and CHANGELOG.md must be included in the `.vsix` for the Marketplace to display them. Development-only files (tests, node_modules, built .vsix packages) should be excluded to minimize package size.

## R4: Icon Requirements

**Decision**: Create a 256x256 PNG icon at `images/icon.png`

**Requirements**:
- Format: PNG (recommended; SVG has restrictions)
- Minimum size: 128x128 pixels
- Recommended size: 256x256 pixels (Retina support)
- Should be recognizable at small sizes (16x16 in sidebar)
- Works on both light and dark VS Code themes

**Rationale**: 256x256 is the recommended size for Retina display support. PNG is universally supported with no restrictions.

## R5: README Content Strategy

**Decision**: Create README.md in Japanese (primary) with English section headers for international discoverability

**Structure**:
1. Extension name and brief description (bilingual)
2. Features list: file type support, syntax highlighting, snippets
3. Supported file extensions table
4. Snippet usage example
5. Requirements (VS Code version)
6. License reference

**Rationale**: The primary audience is the Japanese AviUtl community (per spec Assumptions). English section headers help with Marketplace search indexing while Japanese body text serves the target audience.

## R6: License Choice

**Decision**: MIT License

**Rationale**: Spec assumption states "MIT or similar permissive open-source license". MIT is the most common choice for VS Code extensions and editor packages. The existing repository is public on GitHub with no existing license file — MIT provides maximum permissiveness for the AviUtl community.

## R7: CHANGELOG Format

**Decision**: Follow [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) format

**Rationale**: This is the de facto standard for VS Code extensions and is referenced in VS Code's own documentation. VS Code Marketplace automatically renders CHANGELOG.md on the extension detail page.
