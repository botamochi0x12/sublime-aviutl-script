# Data Model: CI/CD Pipeline

**Feature Branch**: `006-ci-cd`
**Date**: 2026-02-15

## Overview

This feature has no application-level data entities. It consists entirely of GitHub Actions workflow configuration (YAML) and interacts with GitHub's API through declarative actions.

## Workflow Artifacts

The only data artifact produced is the `.vsix` file:

| Artifact | Format | Produced by | Consumed by | Lifecycle |
|----------|--------|-------------|-------------|-----------|
| `*.vsix` | VS Code extension package (ZIP) | `vsce package` in `package` job | `release` job → GitHub Release asset | Ephemeral in CI; persisted as GitHub Release asset |

## State Transitions

```text
Tag pushed → test (pass/fail)
                               → release created (if both pass)
           → package (pass/fail)
```

- **If test fails**: workflow stops; no release created.
- **If package fails**: workflow stops; no release created.
- **If both pass**: `release` job downloads `.vsix` artifact and creates GitHub Release.
