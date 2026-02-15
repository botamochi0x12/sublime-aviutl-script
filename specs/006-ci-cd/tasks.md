# Tasks: CI/CD Pipeline

**Input**: Design documents from `/specs/006-ci-cd/`
**Prerequisites**: plan.md (required), spec.md (required for user stories), research.md

**Tests**: Not explicitly requested. The pipeline itself is declarative YAML — verification is done by pushing to GitHub and observing pipeline execution.

**Organization**: Tasks are grouped by user story. Since all three stories share the same workflow file, US1 creates the file with the `test` job, US2 adds the `push` trigger (already included in US1 by design), and US3 adds the `package` job.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3)
- Include exact file paths in descriptions

---

## Phase 1: Setup

**Purpose**: Create the directory structure for GitHub Actions

- [x] T001 Create `.github/workflows/` directory at repository root

---

## Phase 2: User Story 1 — Automated Quality Gate on Pull Requests (Priority: P1) — MVP

**Goal**: Grammar tests run automatically on every PR targeting `master` and report results as an informational status check.

**Independent Test**: Open a PR against `master` on GitHub; verify the `test` job runs, passes, and reports status on the PR.

### Implementation for User Story 1

- [x] T002 [US1] Create workflow file `.github/workflows/ci.yml` with `test` job: triggers on `pull_request` targeting `master` and `push` to `master`; runs `checkout`, `setup-node` (lts/*), `npm ci`, and `npm test` with `working-directory: vscode-aviutl-script`

**Checkpoint**: After pushing this branch and opening a PR, the `test` job should run and report grammar test results.

---

## Phase 3: User Story 3 — VS Code Extension Packaging Verification (Priority: P3)

**Goal**: The CI pipeline verifies that the VS Code extension packages successfully and uploads the `.vsix` artifact.

**Independent Test**: Open a PR on GitHub; verify the `package` job runs in parallel with `test`, produces a `.vsix` artifact, and reports status independently.

> Note: US2 (push to `master` trigger) is already satisfied by the trigger configuration in T002. No additional task is needed — the `push: branches: [master]` trigger covers US2's acceptance scenarios. US3 is the next incremental task.

### Implementation for User Story 3

- [x] T003 [US3] Add `package` job to `.github/workflows/ci.yml`: runs `checkout`, `setup-node` (lts/*), `npm ci`, `npx vsce package`, and `actions/upload-artifact` for the `.vsix` file; `working-directory: vscode-aviutl-script`

**Checkpoint**: Both `test` and `package` jobs run in parallel on PRs and pushes to `master`. The `.vsix` artifact is downloadable from the GitHub Actions run.

---

## Phase 4: Polish & Cross-Cutting Concerns (CI)

**Purpose**: Add workflow metadata and give the workflow a descriptive name

- [x] T004 Add workflow `name: CI` and descriptive job names (`Grammar Tests`, `Package Extension`) to `.github/workflows/ci.yml`
- [x] T005 Mark implementation tasks as complete in `specs/006-ci-cd/tasks.md`

---

## Phase 5: User Story 4 — Automated GitHub Release on Tag Push (Priority: P4)

**Goal**: Pushing a `v*` tag automatically creates a GitHub Release with the `.vsix` artifact attached, but only if tests pass.

**Independent Test**: Push a `v*` tag to the repository; verify that a GitHub Release is created with the `.vsix` file attached and auto-generated release notes. Verify that a tag on a broken commit does NOT produce a release.

### Implementation for User Story 4

- [ ] T006 [US4] Create `.github/workflows/release.yml` with workflow name `Release`, trigger on `push: tags: ['v*']`, `permissions: contents: write`, and three jobs: `test` (Grammar Tests), `package` (Package Extension), and `release` (Create GitHub Release). The `test` job: `actions/checkout@v4`, `actions/setup-node@v4` with `node-version: lts/*`, `npm ci`, `npm test`; working-directory `vscode-aviutl-script`. The `package` job: same checkout/setup/install, then `npx vsce package`, then `actions/upload-artifact@v4` with name `aviutl-script-vsix` and path `vscode-aviutl-script/*.vsix`; working-directory `vscode-aviutl-script`. The `release` job: `needs: [test, package]`, `actions/download-artifact@v4` with name `aviutl-script-vsix`, then `softprops/action-gh-release@v2` with `generate_release_notes: true` and `files: '*.vsix'`.
- [ ] T007 [US4] Verify `.github/workflows/release.yml` YAML syntax is valid (run `npx yaml-lint` or validate structure manually)

**Checkpoint**: After merging and pushing a `v*` tag, the release workflow should run: `test` and `package` in parallel, then `release` creates a GitHub Release with `.vsix` attached.

---

## Phase 6: Polish & Cross-Cutting Concerns (CD)

**Purpose**: Final documentation updates

- [ ] T008 Mark all US4 implementation tasks as complete in `specs/006-ci-cd/tasks.md`

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies — can start immediately ✅
- **US1 (Phase 2)**: Depends on Phase 1 (directory must exist) ✅
- **US3 (Phase 3)**: Depends on Phase 2 (workflow file must exist to add the `package` job) ✅
- **Polish/CI (Phase 4)**: Depends on Phase 3 (all CI jobs must exist before naming them) ✅
- **US4 (Phase 5)**: Depends on Phase 1 (`.github/workflows/` directory exists). Independent of `ci.yml` — creates a new file `release.yml`.
- **Polish/CD (Phase 6)**: Depends on Phase 5

### User Story Dependencies

- **User Story 1 (P1)**: Creates the CI workflow file with `test` job and both triggers → MVP ✅
- **User Story 2 (P2)**: Fully satisfied by US1's trigger configuration ✅
- **User Story 3 (P3)**: Adds the `package` job to `ci.yml` ✅
- **User Story 4 (P4)**: Creates a NEW workflow file `release.yml` — independent of `ci.yml`

### Parallel Opportunities

- T006 creates a new file (`release.yml`), so it has no file-level conflicts with existing CI tasks.
- T006 and T007 are sequential (T007 validates what T006 created).

---

## Implementation Strategy

### MVP First (User Story 1 Only)

1. Complete Phase 1: Setup (T001) ✅
2. Complete Phase 2: User Story 1 (T002) ✅
3. **STOP and VALIDATE**: Push branch, open PR, verify `test` job runs ✅

### Incremental Delivery

1. T001 → Directory exists ✅
2. T002 → Grammar tests run on PRs and pushes to master (US1 + US2 complete) ✅
3. T003 → Packaging verification added (US3 complete) ✅
4. T004–T005 → CI polish complete ✅
5. T006–T007 → CD release workflow added (US4 complete)
6. T008 → CD polish and close out

---

## Notes

- CI tasks (T001–T005) modify `ci.yml` — all complete
- CD tasks (T006–T008) create and validate a new file `release.yml` — independent of `ci.yml`
- US2 is a zero-task user story because the `push` trigger is part of US1's workflow configuration
- Commit after each task per CLAUDE.md conventions
- Total: 8 tasks across 6 phases (5 CI complete, 3 CD pending)
