# Contract: Release Workflow (`release.yml`)

**Feature Branch**: `006-ci-cd`
**Date**: 2026-02-15

## Trigger

```yaml
on:
  push:
    tags: ['v*']
```

## Permissions

```yaml
permissions:
  contents: write
```

## Jobs

### Job 1: `test`

| Property | Value |
|----------|-------|
| Name | Grammar Tests |
| Runner | `ubuntu-latest` |
| Working directory | `vscode-aviutl-script` |
| Steps | `actions/checkout@v4` → `actions/setup-node@v4` (lts/*) → `npm ci` → `npm test` |
| Depends on | — |

### Job 2: `package`

| Property | Value |
|----------|-------|
| Name | Package Extension |
| Runner | `ubuntu-latest` |
| Working directory | `vscode-aviutl-script` |
| Steps | `actions/checkout@v4` → `actions/setup-node@v4` (lts/*) → `npm ci` → `npx vsce package` → `actions/upload-artifact@v4` |
| Depends on | — |
| Artifact name | `aviutl-script-vsix` |
| Artifact path | `vscode-aviutl-script/*.vsix` |

### Job 3: `release`

| Property | Value |
|----------|-------|
| Name | Create GitHub Release |
| Runner | `ubuntu-latest` |
| Steps | `actions/download-artifact@v4` (name: `aviutl-script-vsix`) → `softprops/action-gh-release@v2` |
| Depends on | `test`, `package` |
| Release notes | Auto-generated (`generate_release_notes: true`) |
| Release files | `*.vsix` |

## Expected Behavior

1. Maintainer pushes a `v*` tag (e.g., `git tag v0.1.0 && git push origin v0.1.0`)
2. `test` and `package` jobs start in parallel
3. If either fails → workflow fails, no release created
4. If both pass → `release` job downloads `.vsix` from `package` artifact
5. `release` job creates a GitHub Release with:
   - Tag name as release title
   - Auto-generated release notes
   - `.vsix` file attached as a downloadable asset
