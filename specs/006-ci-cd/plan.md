# Implementation Plan: CI/CD Pipeline

**Branch**: `006-ci-cd` | **Date**: 2026-02-15 | **Spec**: [spec.md](spec.md)
**Input**: Feature specification from `/specs/006-ci-cd/spec.md`

## Summary

Add a GitHub Actions CI pipeline that automatically runs VS Code grammar tests (`vscode-tmgrammar-test`) on every pull request and push to `master`, and verifies VS Code extension packaging via `vsce package`. The pipeline reports results as informational status checks (non-blocking). No branch protection rules or automated publishing are in scope.

## Technical Context

**Language/Version**: YAML (GitHub Actions workflow); Node.js latest LTS (for `npm test` and `vsce package`)
**Primary Dependencies**: GitHub Actions (hosted CI); `vscode-tmgrammar-test` ^0.1.3 (existing devDep); `@vscode/vsce` ^3.7.1 (existing devDep)
**Storage**: N/A
**Testing**: `npm test` in `vscode-aviutl-script/` runs `vscode-tmgrammar-test`; `npx vsce package` verifies packaging
**Target Platform**: GitHub-hosted Ubuntu runner
**Project Type**: Single — declarative CI configuration (YAML files only)
**Performance Goals**: Pipeline completes within 5 minutes (SC-002)
**Constraints**: Free tier GitHub Actions; no secrets or tokens required; no branch protection changes
**Scale/Scope**: Single workflow file, 2 jobs (test + package)

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

| Principle | Status | Notes |
|-----------|--------|-------|
| I. Simplicity (YAGNI) | PASS | Single workflow file with minimal configuration. No abstractions, no reusable workflows, no matrix builds — the simplest viable approach. |
| II. Test-First (TDD) | PASS | The pipeline itself is declarative YAML, not behavioral code. TDD applies to grammar changes tested by the pipeline, not to the pipeline config itself. Verification will be done by pushing a test PR. |
| III. Syntax Fidelity | N/A | This feature does not alter any grammar scopes. |
| IV. Backward Compatibility | PASS | No changes to Sublime Text files. The `.github/` directory is already in `.vscodeignore`. |
| V. Single Source of Truth | N/A | No keyword changes. |
| Keyword Accuracy | N/A | No keyword changes. |
| Quality Gates | PASS | This feature directly automates the "Syntax tests pass" quality gate from the constitution. |

All gates pass. No violations to justify.

## Project Structure

### Documentation (this feature)

```text
specs/006-ci-cd/
├── plan.md              # This file
├── research.md          # Phase 0 output
├── spec.md              # Feature specification
├── checklists/
│   └── requirements.md  # Spec quality checklist
└── tasks.md             # Phase 2 output (created by /speckit.tasks)
```

### Source Code (repository root)

```text
.github/
└── workflows/
    └── ci.yml           # GitHub Actions workflow (single file)
```

**Structure Decision**: A single workflow file at `.github/workflows/ci.yml` is all that's needed. No `contracts/`, `data-model.md`, or `quickstart.md` are applicable — this feature is pure CI configuration with no data model, API surface, or application code.

## Design

### Workflow Architecture

The workflow file `ci.yml` contains:

**Triggers**:
- `pull_request` targeting `master` branch
- `push` to `master` branch

**Jobs**:

1. **`test`** — Grammar test suite
   - Runs on: `ubuntu-latest`
   - Steps: checkout → setup Node.js (latest LTS) → `npm ci` (in `vscode-aviutl-script/`) → `npm test`
   - Working directory: `vscode-aviutl-script/`

2. **`package`** — Extension packaging verification
   - Runs on: `ubuntu-latest`
   - Steps: checkout → setup Node.js (latest LTS) → `npm ci` (in `vscode-aviutl-script/`) → `npx vsce package` → upload `.vsix` as artifact
   - Working directory: `vscode-aviutl-script/`
   - The two jobs run in parallel (no dependency between them) for faster feedback.

### Key Decisions

- **`npm ci` over `npm install`**: Ensures reproducible installs from lockfile, appropriate for CI.
- **No caching**: The dependency tree is small (~260 packages). Adding cache configuration would violate Principle I (YAGNI) for minimal time savings.
- **No matrix builds**: Single Node.js LTS version per the clarified FR-005. A matrix would add complexity without clear benefit for this project.
- **No branch protection**: Per clarification, checks are informational only. No GitHub API calls or settings changes needed.
- **Artifact upload**: The `.vsix` file is uploaded as a GitHub Actions artifact so it can be inspected if needed, but is not published.

## Complexity Tracking

No constitution violations. Table not needed.
