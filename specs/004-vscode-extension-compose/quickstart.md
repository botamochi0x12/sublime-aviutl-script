# Quickstart: Compose VS Code Extension

**Feature**: [spec.md](spec.md) | **Date**: 2026-02-15

## Prerequisites

- Node.js >=20.x
- npm
- VS Code (for manual testing)

## Setup

```bash
cd vscode-aviutl-script
npm install
```

## Development Workflow

### Run grammar tests

```bash
npm test
```

### Package the extension

```bash
npx @vscode/vsce package
```

This creates `aviutl-script-<version>.vsix` in the current directory.

### Verify package contents

```bash
npx @vscode/vsce ls
```

Lists all files that will be included in the `.vsix` package. Verify that:
- README.md, CHANGELOG.md, LICENSE are included
- test/, node_modules/, *.vsix are excluded

### Install locally for testing

```bash
code --install-extension aviutl-script-0.1.0.vsix
```

### Publish to Marketplace

```bash
npx @vscode/vsce login <publisher-id>
npx @vscode/vsce publish
```

> **Note**: Requires a Personal Access Token from the [VS Code Marketplace](https://marketplace.visualstudio.com/manage). Publisher account creation is outside this feature's scope.

## Key Files

| File | Purpose |
|------|---------|
| `package.json` | Extension manifest with Marketplace metadata |
| `.vscodeignore` | Controls which files are included/excluded from `.vsix` |
| `README.md` | Displayed on Marketplace detail page |
| `CHANGELOG.md` | Version history shown on Marketplace |
| `LICENSE` | MIT license file |
| `images/icon.png` | Extension icon (256x256 PNG) |

## Testing Checklist

1. `npm test` — all grammar assertions pass
2. `npx @vscode/vsce ls` — README.md, CHANGELOG.md, LICENSE included; test/, node_modules/ excluded
3. `npx @vscode/vsce package` — `.vsix` created without errors
4. Install `.vsix` in VS Code — extension appears with correct name, icon, and description
5. Open a `.obj` file — syntax highlighting activates as "AviUtl Script (Lua)"
6. Type `obj.` — snippets appear in autocomplete
