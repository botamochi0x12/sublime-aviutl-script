# Research: CI/CD Pipeline

**Feature Branch**: `006-ci-cd`
**Date**: 2026-02-15

## CI Decisions (unchanged from prior research)

### Decision 1: CI Platform — GitHub Actions

- **Decision**: Use GitHub Actions
- **Rationale**: Repository is hosted on GitHub. GitHub Actions is free for public repositories, requires no external service setup, and is the standard choice for GitHub-hosted open-source projects.
- **Alternatives considered**:
  - CircleCI: Free tier available but requires external account setup. No advantage for this use case.
  - Travis CI: Free tier limitations for open-source changed in recent years. Less predictable.

### Decision 2: Node.js Version Strategy — Latest LTS

- **Decision**: Use `lts/*` version specifier in `setup-node` action
- **Rationale**: Per FR-005, latest LTS only. The `lts/*` alias automatically resolves to the current LTS release without needing manual version bumps.
- **Alternatives considered**:
  - Pinned version (e.g., `20`): Requires manual updates when LTS changes.
  - Matrix (`[20, 22]`): Adds complexity, doubles CI time, no clear benefit for a declarative extension package.

### Decision 3: Dependency Installation — `npm ci`

- **Decision**: Use `npm ci` instead of `npm install`
- **Rationale**: `npm ci` is designed for CI environments — it installs from `package-lock.json` exactly, fails if lockfile is out of sync, and is faster because it skips the dependency resolution step.
- **Alternatives considered**:
  - `npm install`: May modify lockfile, less reproducible.

### Decision 4: CI Workflow Structure — Two Parallel Jobs

- **Decision**: Two independent jobs (`test` and `package`) running in parallel
- **Rationale**: Jobs have no dependency on each other. Parallel execution provides faster feedback. Independent reporting allows contributors to see exactly which step failed.
- **Alternatives considered**:
  - Single job with sequential steps: Simpler YAML but slower.
  - Package depends on test: Unnecessary coupling.

## CD Decisions (new — GitHub Release)

### Decision 5: GitHub Release Action — `softprops/action-gh-release@v2`

- **Decision**: Use `softprops/action-gh-release@v2`
- **Rationale**: Most popular community action for GitHub Releases. Actively maintained (v2.5.0, Dec 2024). Supports `generate_release_notes: true` and `files` glob for artifact upload. Works with default `GITHUB_TOKEN` + `contents: write`. Minimal configuration (~5 lines).
- **Alternatives considered**:
  - `ncipollo/release-action`: More features than needed; adds unnecessary complexity for a simple VSIX upload. Violates Principle I (YAGNI).
  - `gh release create` (CLI): Viable but requires more shell scripting. The action is more declarative and idiomatic for GitHub Actions.
  - Manual releases: Violates FR-010 (automated release creation).

### Decision 6: Workflow Permissions — `contents: write`

- **Decision**: Use `permissions: contents: write` at workflow level
- **Rationale**: The `GITHUB_TOKEN` is automatically available on GitHub-hosted runners. Setting `contents: write` grants the release action permission to create releases and upload assets. No repository secrets configuration required.
- **Alternatives considered**:
  - Personal Access Token (PAT): Requires secret management; unnecessary for public repo releases.
  - Job-level permissions: Equivalent result, but workflow-level is simpler when only one release job exists.

### Decision 7: CD Workflow Structure — Sequential `test` → `package` → `release`

- **Decision**: Three jobs with `needs:` dependencies. `test` and `package` run in parallel; `release` depends on both via `needs: [test, package]`.
- **Rationale**: FR-009 requires tests to pass before release. Using `needs:` ensures the release only runs after both gates pass. Matches the existing `ci.yml` job pattern (separate `test` and `package` jobs).
- **Alternatives considered**:
  - Single job (all steps): Conflates concerns; harder to read failure reports.
  - Reusable workflow (call `ci.yml`): Over-engineering for this scope. Duplicating ~10 lines of test/package steps is acceptable per Principle I.

### Decision 8: Artifact Passing Between Jobs

- **Decision**: Use `actions/upload-artifact@v4` in `package` job and `actions/download-artifact@v4` in `release` job
- **Rationale**: GitHub Actions jobs run on separate runners. The `.vsix` file must be transferred via the artifact mechanism. This is the standard pattern.

### Decision 9: Tag-to-Version Mapping

- **Decision**: The `v*` tag name becomes the release title automatically. No version extraction logic.
- **Rationale**: `softprops/action-gh-release` defaults to using the tag name as the release name. The `package.json` version should match the tag (maintainer responsibility, not enforced by CI). Enforcing version match would add complexity with no clear benefit for this project size (Principle I).
