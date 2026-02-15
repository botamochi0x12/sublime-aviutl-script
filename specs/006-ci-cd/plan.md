# Implementation Plan: CI/CD Pipeline

**Branch**: `006-ci-cd` | **Date**: 2026-02-15 | **Spec**: [spec.md](spec.md)
**Input**: Feature specification from `/specs/006-ci-cd/spec.md`

## Summary

Add a CD pipeline (GitHub Actions) that creates a GitHub Release with a `.vsix` artifact when a `v*` tag is pushed. The CI pipeline (`ci.yml`) already exists and covers grammar tests + packaging verification on PRs and main-branch pushes. This plan adds a separate `release.yml` workflow that re-runs tests, packages the extension, and publishes a GitHub Release with auto-generated release notes.

## Technical Context

**Language/Version**: YAML (GitHub Actions workflow); Node.js latest LTS (for `npm test` and `vsce package`)
**Primary Dependencies**: `vscode-tmgrammar-test` ^0.1.3 (existing devDep); `@vscode/vsce` ^3.7.1 (existing devDep); GitHub Actions (`actions/checkout@v4`, `actions/setup-node@v4`, `actions/upload-artifact@v4`, `softprops/action-gh-release`)
**Storage**: N/A
**Testing**: `npm test` in `vscode-aviutl-script/` (runs `vscode-tmgrammar-test`); workflow validation via `actionlint` or manual tag push
**Target Platform**: GitHub Actions (ubuntu-latest runner)
**Project Type**: Single project — declarative VS Code extension with CI/CD workflows
**Performance Goals**: CI/CD pipeline completes under 10 minutes (FR-006)
**Constraints**: Free for open-source (GitHub Actions free tier); no secrets or PATs required — uses default `GITHUB_TOKEN` with `contents: write`
**Scale/Scope**: Single workflow file addition (~40 lines YAML)

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

| Principle | Status | Notes |
|-----------|--------|-------|
| I. Simplicity (YAGNI) | PASS | Single YAML file, no abstractions. Reuses existing `npm test` and `vsce package` commands. No wrapper scripts. |
| II. Test-First (TDD) | PASS | The CD workflow itself runs tests before release (FR-009). The workflow YAML is validated by pushing a test tag. |
| III. Syntax Fidelity | N/A | CI/CD infrastructure does not modify grammar scopes. |
| IV. Backward Compatibility | PASS | Additive change only — new file `release.yml`. No modifications to existing `ci.yml` or Sublime package files. |
| V. Single Source of Truth | PASS | No keyword lists involved. The workflow references the same `npm test` command as CI. |

**Pre-design gate result**: PASS — no violations. Proceed to Phase 0.

### Post-Design Re-check (after Phase 1)

| Principle | Status | Notes |
|-----------|--------|-------|
| I. Simplicity (YAGNI) | PASS | Single YAML file (~50 lines). Reuses existing commands. Research rejected over-engineered alternatives (reusable workflows, version enforcement). |
| II. Test-First (TDD) | PASS | Release workflow runs tests before release (FR-009). Workflow validated by tag push. |
| III. Syntax Fidelity | N/A | No grammar changes. |
| IV. Backward Compatibility | PASS | `ci.yml` untouched. New `release.yml` is purely additive. |
| V. Single Source of Truth | PASS | Same `npm test` command in both workflows. |

**Post-design gate result**: PASS — no new violations introduced by design decisions.

## Project Structure

### Documentation (this feature)

```text
specs/006-ci-cd/
├── plan.md              # This file
├── research.md          # Phase 0 output
├── data-model.md        # Phase 1 output (minimal — no entities)
├── quickstart.md        # Phase 1 output
├── contracts/           # Phase 1 output (workflow contract)
└── tasks.md             # Phase 2 output (/speckit.tasks command)
```

### Source Code (repository root)

```text
.github/
└── workflows/
    ├── ci.yml           # Existing — CI pipeline (tests + packaging on PR/push)
    └── release.yml      # NEW — CD pipeline (tests + package + GitHub Release on v* tag)
```

**Structure Decision**: Single new file at `.github/workflows/release.yml`. No other source files created or modified. The existing `ci.yml` remains untouched.
