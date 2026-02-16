# Tasks: Global Function Snippets

**Input**: Design documents from `/specs/005-global-function-snippets/`
**Prerequisites**: plan.md (required), spec.md (required for user stories), research.md

**Tests**: TDD is mandatory per project constitution (Principle II). Each snippet is verified via snippet trigger testing in the target editor environment.

**Organization**: Tasks are grouped by user story. All three stories are P1 and independent — they can be implemented in parallel or sequentially.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3)
- Include exact file paths in descriptions

## Path Conventions

- **Sublime snippets**: `sublime-aviutl-script/snippet/function/`
- **VS Code snippets**: `vscode-aviutl-script/snippets/aviutl-script.code-snippets`

---

## Phase 1: User Story 1 - Bitwise Operation Snippets (Priority: P1)

**Goal**: Provide OR, AND, XOR snippets for both Sublime Text and VS Code

**Independent Test**: Type `OR`, `AND`, or `XOR` in an AviUtl script file and trigger snippet expansion — each should expand to `FUNC(a,b)` with tab-stop placeholders.

### Implementation for User Story 1

- [x] T001 [P] [US1] Create OR snippet in `sublime-aviutl-script/snippet/function/OR.sublime-snippet` — scope: `source.lua.aviutl-script`, trigger: `OR`, expansion: `OR(${1:a},${2:b})$0`, description: `OR,AND,XORのビット演算をします。`
- [x] T002 [P] [US1] Create AND snippet in `sublime-aviutl-script/snippet/function/AND.sublime-snippet` — scope: `source.lua.aviutl-script`, trigger: `AND`, expansion: `AND(${1:a},${2:b})$0`, description: `OR,AND,XORのビット演算をします。`
- [x] T003 [P] [US1] Create XOR snippet in `sublime-aviutl-script/snippet/function/XOR.sublime-snippet` — scope: `source.lua.aviutl-script`, trigger: `XOR`, expansion: `XOR(${1:a},${2:b})$0`, description: `OR,AND,XORのビット演算をします。`
- [x] T004 [US1] Add OR, AND, XOR entries to `vscode-aviutl-script/snippets/aviutl-script.code-snippets` — prefix/body/description matching the Sublime snippets above

**Checkpoint**: OR, AND, XOR snippets functional in both editors

---

## Phase 2: User Story 2 - Arithmetic Shift Snippet (Priority: P1)

**Goal**: Provide SHIFT snippet for both Sublime Text and VS Code

**Independent Test**: Type `SHIFT` in an AviUtl script file and trigger snippet expansion — should expand to `SHIFT(a,b)` with tab-stop placeholders.

### Implementation for User Story 2

- [x] T005 [US2] Create SHIFT snippet in `sublime-aviutl-script/snippet/function/SHIFT.sublime-snippet` — scope: `source.lua.aviutl-script`, trigger: `SHIFT`, expansion: `SHIFT(${1:a},${2:b})$0`, description: `算術シフトをします。`
- [x] T006 [US2] Add SHIFT entry to `vscode-aviutl-script/snippets/aviutl-script.code-snippets` — prefix/body/description matching the Sublime snippet above

**Checkpoint**: SHIFT snippet functional in both editors

---

## Phase 3: User Story 3 - Debug Print Snippet (Priority: P1)

**Goal**: Provide debug_print snippet for both Sublime Text and VS Code

**Independent Test**: Type `debug_print` in an AviUtl script file and trigger snippet expansion — should expand to `debug_print(str)` with tab-stop placeholder.

### Implementation for User Story 3

- [x] T007 [US3] Create debug_print snippet in `sublime-aviutl-script/snippet/function/debug_print.sublime-snippet` — scope: `source.lua.aviutl-script`, trigger: `debug_print`, expansion: `debug_print(${1:str})$0`, description: `デバック用の表示に使用します。`
- [x] T008 [US3] Add debug_print entry to `vscode-aviutl-script/snippets/aviutl-script.code-snippets` — prefix/body/description matching the Sublime snippet above

**Checkpoint**: debug_print snippet functional in both editors

---

## Phase 4: Polish & Cross-Cutting Concerns

**Purpose**: Regression check and final validation

- [x] T009 Run `vscode-tmgrammar-test` to confirm no grammar regressions in `vscode-aviutl-script/`

---

## Dependencies & Execution Order

### Phase Dependencies

- **Phase 1 (US1)**: No dependencies — can start immediately
- **Phase 2 (US2)**: No dependencies — can start immediately (parallel with Phase 1)
- **Phase 3 (US3)**: No dependencies — can start immediately (parallel with Phase 1, 2)
- **Phase 4 (Polish)**: Depends on all user stories being complete

### User Story Dependencies

- **User Story 1 (P1)**: Independent — OR, AND, XOR snippets
- **User Story 2 (P1)**: Independent — SHIFT snippet
- **User Story 3 (P1)**: Independent — debug_print snippet
- No cross-story dependencies. The only shared file is `aviutl-script.code-snippets` (VS Code), so T004, T006, T008 should run sequentially to avoid merge conflicts.

### Parallel Opportunities

Within User Story 1:
- T001, T002, T003 can all run in parallel (separate `.sublime-snippet` files)
- T004 modifies a shared file and should run after T001–T003

Across stories:
- Sublime snippet tasks (T001–T003, T005, T007) are all independent files and can run in parallel
- VS Code tasks (T004, T006, T008) all modify the same JSON file — run sequentially

---

## Parallel Example: User Story 1

```text
# Launch all Sublime snippet tasks for US1 in parallel:
Task: "Create OR.sublime-snippet"   (T001)
Task: "Create AND.sublime-snippet"  (T002)
Task: "Create XOR.sublime-snippet"  (T003)

# Then append to VS Code snippets:
Task: "Add OR, AND, XOR to aviutl-script.code-snippets" (T004)
```

---

## Implementation Strategy

### MVP First (All Stories — Small Feature)

This is a small, well-defined feature. All 3 user stories are P1 and can be delivered together:

1. Complete Phase 1: OR, AND, XOR snippets (Sublime + VS Code)
2. Complete Phase 2: SHIFT snippet (Sublime + VS Code)
3. Complete Phase 3: debug_print snippet (Sublime + VS Code)
4. Complete Phase 4: Regression test
5. **VALIDATE**: All 5 snippets expand correctly in both editors

### Recommended Execution Order

Since VS Code tasks share a file, the most efficient order is:

1. Create all 5 Sublime `.sublime-snippet` files (T001–T003, T005, T007 — all parallel)
2. Add all 5 VS Code entries at once (T004 + T006 + T008 — batch into single edit)
3. Run regression tests (T009)

---

## Notes

- [P] tasks = different files, no dependencies
- [Story] label maps task to specific user story for traceability
- Commit after each task per CLAUDE.md convention
- All snippet content is fully specified in plan.md — no additional research needed
- Follow existing snippet format exactly (see `sublime-aviutl-script/snippet/function/draw.sublime-snippet` as reference)
