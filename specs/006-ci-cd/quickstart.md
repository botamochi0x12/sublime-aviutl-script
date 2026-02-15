# Quickstart: CI/CD Pipeline — CD Release

**Feature Branch**: `006-ci-cd`

## What's Being Built

A GitHub Actions workflow file (`.github/workflows/release.yml`) that automatically creates a GitHub Release with the VS Code extension `.vsix` artifact when a `v*` tag is pushed.

## Prerequisites

- The CI workflow (`.github/workflows/ci.yml`) already exists and handles tests + packaging on PRs/pushes
- `vscode-aviutl-script/package.json` has `npm test` and `vsce package` working
- Repository is public on GitHub (free Actions minutes)

## Implementation Steps

1. Create `.github/workflows/release.yml` with:
   - Trigger: `push.tags: ['v*']`
   - Permissions: `contents: write`
   - Jobs: `test` (grammar tests), `package` (vsce package + upload artifact), `release` (download artifact + create GitHub Release)
2. The `release` job uses `softprops/action-gh-release@v2` with `generate_release_notes: true` and `files: '*.vsix'`

## How to Test

```bash
# Create and push a tag to trigger the release workflow
git tag v0.1.0
git push origin v0.1.0
```

Then verify:
- The workflow runs in the Actions tab
- A GitHub Release appears with auto-generated notes
- The `.vsix` file is attached and downloadable

## Files Changed

| File | Action | Description |
|------|--------|-------------|
| `.github/workflows/release.yml` | CREATE | New CD workflow for tag-triggered releases |
