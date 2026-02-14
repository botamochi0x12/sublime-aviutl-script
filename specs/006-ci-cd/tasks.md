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

- [ ] T002 [US1] Create workflow file `.github/workflows/ci.yml` with `test` job: triggers on `pull_request` targeting `master` and `push` to `master`; runs `checkout`, `setup-node` (lts/*), `npm ci`, and `npm test` with `working-directory: vscode-aviutl-script`

**Checkpoint**: After pushing this branch and opening a PR, the `test` job should run and report grammar test results.

---

## Phase 3: User Story 3 — VS Code Extension Packaging Verification (Priority: P3)

**Goal**: The CI pipeline verifies that the VS Code extension packages successfully and uploads the `.vsix` artifact.

**Independent Test**: Open a PR on GitHub; verify the `package` job runs in parallel with `test`, produces a `.vsix` artifact, and reports status independently.

> Note: US2 (push to `master` trigger) is already satisfied by the trigger configuration in T002. No additional task is needed — the `push: branches: [master]` trigger covers US2's acceptance scenarios. US3 is the next incremental task.

### Implementation for User Story 3

- [ ] T003 [US3] Add `package` job to `.github/workflows/ci.yml`: runs `checkout`, `setup-node` (lts/*), `npm ci`, `npx vsce package`, and `actions/upload-artifact` for the `.vsix` file; `working-directory: vscode-aviutl-script`

**Checkpoint**: Both `test` and `package` jobs run in parallel on PRs and pushes to `master`. The `.vsix` artifact is downloadable from the GitHub Actions run.

---

## Phase 4: Polish & Cross-Cutting Concerns

**Purpose**: Add workflow metadata and give the workflow a descriptive name

- [ ] T004 Add workflow `name: CI` and descriptive job names (`Grammar Tests`, `Package Extension`) to `.github/workflows/ci.yml`
- [ ] T005 Mark implementation tasks as complete in `specs/006-ci-cd/tasks.md`

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies — can start immediately
- **US1 (Phase 2)**: Depends on Phase 1 (directory must exist)
- **US3 (Phase 3)**: Depends on Phase 2 (workflow file must exist to add the `package` job)
- **Polish (Phase 4)**: Depends on Phase 3 (all jobs must exist before naming them)

### User Story Dependencies

- **User Story 1 (P1)**: Creates the workflow file with `test` job and both triggers → MVP
- **User Story 2 (P2)**: Fully satisfied by US1's trigger configuration (`push: branches: [master]`) — no additional task needed
- **User Story 3 (P3)**: Adds the `package` job to the existing workflow file → depends on US1

### Parallel Opportunities

- T002 and T003 modify the same file (`.github/workflows/ci.yml`), so they MUST be sequential.
- No parallelization is possible for this feature — all tasks target the same single file.

---

## Implementation Strategy

### MVP First (User Story 1 Only)

1. Complete Phase 1: Setup (T001)
2. Complete Phase 2: User Story 1 (T002)
3. **STOP and VALIDATE**: Push branch, open PR, verify `test` job runs
4. This also validates US2 (push trigger) is configured

### Incremental Delivery

1. T001 → Directory exists
2. T002 → Grammar tests run on PRs and pushes to master (US1 + US2 complete)
3. T003 → Packaging verification added (US3 complete)
4. T004–T005 → Polish and close out

---

## Notes

- All tasks modify a single file (`.github/workflows/ci.yml`) — no parallel execution possible
- US2 is a zero-task user story because the `push` trigger is naturally part of the same workflow configuration as the `pull_request` trigger
- Commit after each task per CLAUDE.md conventions
- Total: 5 tasks across 4 phases
