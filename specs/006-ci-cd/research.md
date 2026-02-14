# Research: CI/CD Pipeline

**Feature Branch**: `006-ci-cd`
**Date**: 2026-02-15

## No Unknowns to Resolve

The Technical Context had no NEEDS CLARIFICATION items. All technology choices are straightforward:

### Decision 1: CI Platform — GitHub Actions

- **Decision**: Use GitHub Actions
- **Rationale**: Repository is hosted on GitHub. GitHub Actions is free for public repositories, requires no external service setup, and is the standard choice for GitHub-hosted open-source projects.
- **Alternatives considered**:
  - CircleCI: Free tier available but requires external account setup. No advantage for this use case.
  - Travis CI: Free tier limitations for open-source changed in recent years. Less predictable.
  - None of these alternatives offer benefits that justify the additional setup complexity.

### Decision 2: Node.js Version Strategy — Latest LTS

- **Decision**: Use `lts/*` version specifier in `setup-node` action
- **Rationale**: Per FR-005 clarification, latest LTS only. The `lts/*` alias automatically resolves to the current LTS release without needing manual version bumps.
- **Alternatives considered**:
  - Pinned version (e.g., `20`): Requires manual updates when LTS changes.
  - Matrix (`[20, 22]`): Adds complexity, doubles CI time, no clear benefit for a declarative extension package.

### Decision 3: Dependency Installation — `npm ci`

- **Decision**: Use `npm ci` instead of `npm install`
- **Rationale**: `npm ci` is designed for CI environments — it installs from `package-lock.json` exactly, fails if lockfile is out of sync, and is faster because it skips the dependency resolution step.
- **Alternatives considered**:
  - `npm install`: May modify lockfile, less reproducible.

### Decision 4: Workflow Structure — Two Parallel Jobs

- **Decision**: Two independent jobs (`test` and `package`) running in parallel
- **Rationale**: Jobs have no dependency on each other. Parallel execution provides faster feedback. Independent reporting allows contributors to see exactly which step failed.
- **Alternatives considered**:
  - Single job with sequential steps: Simpler YAML but slower. A packaging failure would not be distinguishable from a test failure at the job level.
  - Package depends on test: Unnecessary coupling — packaging doesn't need test results.
