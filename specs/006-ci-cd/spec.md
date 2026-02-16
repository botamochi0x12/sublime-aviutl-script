# Feature Specification: CI/CD Pipeline

**Feature Branch**: `006-ci-cd`
**Created**: 2026-02-15
**Status**: Draft
**Input**: User description: "Include CI/CD"

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Automated Quality Gate on Pull Requests (Priority: P1)

As a contributor, I want every pull request to automatically run grammar tests so that broken syntax definitions or snippet regressions are caught before merging.

**Why this priority**: This is the core value of CI/CD for this project — preventing regressions from reaching the main branch. Without automated testing on PRs, contributors must manually verify changes, which is error-prone and slows down review.

**Independent Test**: Can be fully tested by opening a PR with a passing test suite and verifying the pipeline runs and reports success, then opening a PR with a deliberately broken grammar and verifying the pipeline blocks merging.

**Acceptance Scenarios**:

1. **Given** a contributor opens a pull request with valid grammar changes, **When** the CI pipeline runs, **Then** all grammar tests pass and the PR is marked as passing.
2. **Given** a contributor opens a pull request with a syntax regression, **When** the CI pipeline runs, **Then** the grammar tests fail and the PR is marked as failing with a clear error report.
3. **Given** a contributor pushes additional commits to an open PR, **When** the new commits are pushed, **Then** the CI pipeline runs again against the latest commit.

---

### User Story 2 - Automated Quality Gate on Main Branch (Priority: P2)

As a maintainer, I want every push to the main branch (including merged PRs) to run the full test suite so that the main branch is always in a verified-good state.

**Why this priority**: Ensures the main branch remains stable after merges, catching any issues that could slip through PR-based testing (e.g., merge conflicts that introduce regressions).

**Independent Test**: Can be fully tested by merging a PR into the main branch and verifying the pipeline runs automatically and reports results.

**Acceptance Scenarios**:

1. **Given** a PR is merged into the main branch, **When** the merge commit lands, **Then** the CI pipeline runs the full test suite against the main branch.
2. **Given** a direct push to the main branch occurs, **When** the push is received, **Then** the CI pipeline runs and reports results.

---

### User Story 3 - VS Code Extension Packaging Verification (Priority: P3)

As a maintainer, I want the CI pipeline to verify that the VS Code extension can be successfully packaged so that packaging issues are caught before release.

**Why this priority**: The project includes a VS Code extension with `@vscode/vsce` for packaging. Verifying the extension packages correctly ensures that changes don't break the distribution artifact, even though publishing is done manually.

**Independent Test**: Can be fully tested by pushing a change to the VS Code extension directory and verifying that the pipeline attempts to package the extension and reports success or failure.

**Acceptance Scenarios**:

1. **Given** a PR includes changes to the VS Code extension, **When** the CI pipeline runs, **Then** the extension is packaged successfully and the resulting `.vsix` artifact is available.
2. **Given** a PR introduces a packaging error (e.g., invalid manifest), **When** the CI pipeline runs, **Then** the packaging step fails and the error is clearly reported.

---

### User Story 4 - Automated GitHub Release on Tag Push (Priority: P4)

As a maintainer, I want pushing a `v*` tag to automatically create a GitHub Release with the `.vsix` artifact so that users can download the extension without manual release steps.

**Why this priority**: Builds on the existing CI pipeline (testing + packaging). Once tests and packaging are verified, automating the release creation eliminates manual steps and ensures every tagged version has a downloadable artifact.

**Independent Test**: Can be fully tested by pushing a `v*` tag to the repository and verifying that a GitHub Release is created with the `.vsix` file attached, and that pushing a tag on a broken commit does NOT create a release.

**Acceptance Scenarios**:

1. **Given** a maintainer pushes a `v*` tag (e.g., `v0.1.0`), **When** the CD pipeline runs and all tests pass, **Then** a GitHub Release is created for that tag with the `.vsix` artifact attached.
2. **Given** a maintainer pushes a `v*` tag, **When** the CD pipeline runs and tests fail, **Then** no GitHub Release is created and the failure is reported.
3. **Given** a GitHub Release is created, **When** a user visits the release page, **Then** the `.vsix` file is available for download.

---

### Edge Cases

- What happens when tests pass but the extension packaging fails (or vice versa)? Each step should report independently so the contributor can identify the exact failure.
- What happens when the CI service is unavailable? PRs should not be blocked indefinitely — the pipeline status should time out gracefully, and maintainers can re-trigger manually.
- What happens when a PR only changes documentation or spec files? The pipeline should still run (to keep behavior predictable) but will pass quickly since no grammar changes are involved.
- What happens when a `v*` tag is pushed but tests fail? No GitHub Release is created; the workflow reports failure so the maintainer can fix the issue, delete the tag, and re-tag.

## Clarifications

### Session 2026-02-15

- Q: Should the CI status check be required (branch protection) or informational? → A: Informational only — CI runs and reports status, but merging is not blocked by a failing check.
- Q: What should trigger a GitHub Release with the `.vsix` artifact? → A: On git tag push matching `v*` pattern (e.g., `v0.1.0`).
- Q: Should the CD pipeline run tests before creating the GitHub Release? → A: Yes — run tests first; only create the release if tests pass.
- Q: Should the GitHub Release use auto-generated release notes or a fixed template? → A: Auto-generated release notes (GitHub generates from merged PRs/commits since last tag).
- Q: Should the CD workflow live in the same file as CI or a separate file? → A: Separate file (e.g., `release.yml`) — dedicated workflow for tag-triggered releases.
- Q: What permissions does the release workflow need? → A: Default `GITHUB_TOKEN` with `contents: write` (no PAT or extra secrets needed).

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: The CI pipeline MUST run the VS Code grammar test suite (`vscode-tmgrammar-test`) on every pull request targeting the main branch.
- **FR-002**: The CI pipeline MUST run the VS Code grammar test suite on every push to the main branch.
- **FR-003**: The CI pipeline MUST attempt to package the VS Code extension using `vsce package` and report success or failure.
- **FR-004**: The CI pipeline MUST report test and packaging results back to the pull request as an informational status check (not a required/blocking check for merging).
- **FR-005**: The CI pipeline MUST install project dependencies using the latest LTS version of the runtime environment before running tests.
- **FR-006**: The CI pipeline MUST complete within a reasonable time frame (under 10 minutes for a typical run).
- **FR-007**: The CI pipeline MUST use a hosted CI service that is free for open-source projects.
- **FR-008**: The CD pipeline MUST be triggered by a git tag push matching the `v*` pattern.
- **FR-009**: The CD pipeline MUST run the full test suite before creating a release; if tests fail, no release is created.
- **FR-010**: The CD pipeline MUST create a GitHub Release for the pushed tag with the `.vsix` artifact attached.
- **FR-011**: The GitHub Release MUST use auto-generated release notes (derived from merged PRs and commits since the previous tag).
- **FR-012**: The CD workflow MUST use the default `GITHUB_TOKEN` with `contents: write` permission; no Personal Access Token or additional secrets are required.

### Assumptions

- **A-001**: The repository is hosted on GitHub, so GitHub Actions is the natural CI/CD platform (free for public repositories).
- **A-002**: The Sublime Text syntax tests (`Tools > Build` in Sublime) cannot be automated in CI because they require a running Sublime Text instance. Only the VS Code grammar tests (`vscode-tmgrammar-test`) will be automated.
- **A-003**: Extension publishing to the VS Code Marketplace is out of scope. However, the CD pipeline MUST create a GitHub Release with the `.vsix` artifact when a `v*` tag is pushed.
- **A-004**: The existing `npm test` script in `vscode-aviutl-script/package.json` is the canonical test command.
- **A-005**: The CD pipeline lives in a separate workflow file (e.g., `.github/workflows/release.yml`), distinct from the CI workflow (`ci.yml`).

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: 100% of pull requests targeting the main branch receive automated test results before merge.
- **SC-002**: Contributors can see pass/fail status within 5 minutes of pushing to a pull request.
- **SC-003**: No regressions in grammar definitions reach the main branch after CI is enabled.
- **SC-004**: The VS Code extension can be verified as packageable on every PR without manual intervention.
- **SC-005**: Every `v*` tag push that passes tests results in a GitHub Release with a downloadable `.vsix` artifact.
