# Tasks: Migrate AviUtl Syntax Highlighting to VS Code Extension

**Input**: Design documents from `/specs/002-vscode-extension-migration/`
**Prerequisites**: plan.md, spec.md, research.md, data-model.md, contracts/

**Tests**: TDD is mandatory per Constitution Principle II. Grammar scope assertions are written first using `vscode-tmgrammar-test` and must FAIL before implementation (Red-Green-Refactor).

**Organization**: Tasks are grouped by user story to enable independent implementation and testing of each story.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3)
- Include exact file paths in descriptions

---

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Project initialization — create the VS Code extension directory structure and configure dev tooling

- [ ] T001 Create `vscode-aviutl-script/` directory structure per plan.md: `syntaxes/`, `snippets/`, `test/`
- [ ] T002 Create `vscode-aviutl-script/package.json` with extension manifest per contracts/package-json-schema.md (language registration, grammar, snippets contributions, devDependencies, test script)
- [ ] T003 Run `npm install` in `vscode-aviutl-script/` to install `vscode-tmgrammar-test` dev dependency
- [ ] T004 [P] Create `vscode-aviutl-script/.vscodeignore` excluding `test/`, `node_modules/`, and dev files from VSIX package

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Minimal grammar skeleton that all user stories depend on — the empty grammar file that `vscode-tmgrammar-test` can load

**CRITICAL**: No user story work can begin until this phase is complete

- [ ] T005 Create minimal `vscode-aviutl-script/syntaxes/aviutl-script.tmLanguage.json` with `$schema`, `name`, `scopeName` (`source.lua.aviutl-script`), empty `patterns` array with only `{ "include": "source.lua" }`, and empty `repository` object
- [ ] T006 Create `vscode-aviutl-script/test/aviutl-script.test.aviutl-script` with test file header (`-- SYNTAX TEST "source.lua.aviutl-script"`) and a single Lua baseline assertion (e.g., verify a Lua comment scopes as `comment.line.double-dash.lua`)
- [ ] T007 Run `npm test` in `vscode-aviutl-script/` to verify the test infrastructure works (baseline Lua assertion passes)

**Checkpoint**: Test infrastructure works — TDD grammar development can now begin

---

## Phase 3: User Story 1 — AviUtl Script Syntax Highlighting (Priority: P1) MVP

**Goal**: Full syntax highlighting for AviUtl-specific tokens: section labels, global functions, obj.mutable properties, obj.readonly properties, and obj.methods — all inheriting from base Lua grammar

**Independent Test**: Open any AviUtl script file in VS Code Extension Development Host and verify all AviUtl tokens are highlighted with distinct scopes; run `npm test` and all grammar assertions pass

### Tests for User Story 1 (TDD — write FIRST, must FAIL)

> **NOTE: Write these tests FIRST, ensure they FAIL before implementation (Red phase)**

- [ ] T008 [P] [US1] Write grammar test assertions for section labels (`@label_name`) in `vscode-aviutl-script/test/aviutl-script.test.aviutl-script`: assert `@` scopes as `punctuation.definition.keyword.aviutl-script keyword.declaration.extends.aviutl-script`, label name scopes as `entity.name.label.aviutl-script` (spec FR-007)
- [ ] T009 [P] [US1] Write grammar test assertions for AviUtl global functions (`OR`, `AND`, `XOR`, `RGB`, `HSV`, `SHIFT`, `debug_print`) in `vscode-aviutl-script/test/aviutl-script.test.aviutl-script`: assert each scopes as `support.function.library.aviutl-script` (spec FR-003)
- [ ] T010 [P] [US1] Write grammar test assertions for `obj.` mutable properties (`ox`, `oy`, `oz`, `zoom`, `alpha`, `aspect`, `rx`, `ry`, `rz`, `cx`, `cy`, `cz`) in `vscode-aviutl-script/test/aviutl-script.test.aviutl-script`: assert `obj` scopes as `constant.language.aviutl-script support.constant.builtin.aviutl-script`, `.` scopes as `punctuation.accessor.dot.aviutl-script`, property scopes as `variable.other.aviutl-script` (spec FR-004)
- [ ] T011 [P] [US1] Write grammar test assertions for `obj.` read-only properties (`w`, `h`, `screen_w`, `screen_h`, `time`, `frame`, `totalframe`, `framerate`, `x`, `y`, `z`, etc.) in `vscode-aviutl-script/test/aviutl-script.test.aviutl-script`: assert property scopes as `entity.name.constant.aviutl-script` (spec FR-005)
- [ ] T012 [P] [US1] Write grammar test assertions for `obj.` methods (`mes`, `draw`, `load`, `effect`, `rand`, `drawpoly`, `setfont`, `filter`, `setanchor`, `interpolation`, `getaudio`, `setoption`, `getoption`, `getvalue`, `getinfo`, `copybuffer`, `getpixel`, `putpixel`, `copypixel`, `pixeloption`, `getpixeldata`, `putpixeldata`) in `vscode-aviutl-script/test/aviutl-script.test.aviutl-script`: assert method scopes as `support.function.library.aviutl-script` (spec FR-006)
- [ ] T013 [US1] Run `npm test` to verify ALL US1 test assertions FAIL (Red phase confirmation)

### Implementation for User Story 1 (Green phase)

- [ ] T014 [US1] Add `section-label` repository entry to `vscode-aviutl-script/syntaxes/aviutl-script.tmLanguage.json` per data-model.md: pattern `^\s*(\@)(\S+)(?=\s|-|$)`, captures for `@` and label name, and add `#section-label` include to top-level patterns array (before `source.lua`)
- [ ] T015 [US1] Run `npm test` to verify section label assertions pass (Green phase for T008)
- [ ] T016 [US1] Add `aviutl-functions` repository entry to `vscode-aviutl-script/syntaxes/aviutl-script.tmLanguage.json` per data-model.md: pattern `\b(OR|AND|XOR|RGB|HSV|SHIFT|debug_print)\b`, name `support.function.library.aviutl-script`, and add `#aviutl-functions` include to top-level patterns array
- [ ] T017 [US1] Run `npm test` to verify global function assertions pass (Green phase for T009)
- [ ] T018 [US1] Add `obj-mutable-properties` repository entry to `vscode-aviutl-script/syntaxes/aviutl-script.tmLanguage.json` per data-model.md: pattern `\b(obj)\b(\.)(ox|oy|oz|zoom|alpha|aspect|rx|ry|rz|cx|cy|cz)\b`, 3 capture groups with scopes per contracts/grammar-schema.md, and add `#obj-mutable-properties` include to top-level patterns array
- [ ] T019 [US1] Run `npm test` to verify mutable property assertions pass (Green phase for T010)
- [ ] T020 [US1] Add `obj-readonly-properties` repository entry to `vscode-aviutl-script/syntaxes/aviutl-script.tmLanguage.json` per data-model.md: pattern with all read-only property names, 3 capture groups, and add `#obj-readonly-properties` include to top-level patterns array
- [ ] T021 [US1] Run `npm test` to verify read-only property assertions pass (Green phase for T011)
- [ ] T022 [US1] Add `obj-methods` repository entry to `vscode-aviutl-script/syntaxes/aviutl-script.tmLanguage.json` per data-model.md: pattern with all method names, 3 capture groups, and add `#obj-methods` include to top-level patterns array
- [ ] T023 [US1] Run `npm test` to verify ALL US1 assertions pass (complete Green phase for T012 + full regression)

**Checkpoint**: User Story 1 is fully functional — AviUtl syntax highlighting works. This is the MVP.

---

## Phase 4: User Story 2 — AviUtl Code Snippets (Priority: P2)

**Goal**: All 55 code snippets from the Sublime package converted to VS Code format, available via tab completion in AviUtl script files

**Independent Test**: Open an AviUtl script file, type a snippet trigger (e.g., `draw`, `ox`, `!env`), press Tab, and verify the correct code is inserted with appropriate tab stops

### Implementation for User Story 2

- [ ] T024 [P] [US2] Convert the 1 environment template snippet (`!env` / `AviUtl.sublime-snippet`) from Sublime XML to VS Code JSON entry in `vscode-aviutl-script/snippets/aviutl-script.code-snippets`
- [ ] T025 [P] [US2] Convert the 21 method snippets from `snippet/function/*.sublime-snippet` (draw, drawpoly, load, mes, effect, rand, setfont, filter, setanchor, interpolation, getaudio, getoption, getvalue, getinfo, copybuffer, getpixel, putpixel, copypixel, pixeloption, getpixeldata, putpixeldata) to VS Code JSON entries in `vscode-aviutl-script/snippets/aviutl-script.code-snippets`
- [ ] T026 [P] [US2] Convert the 6 setoption variant snippets from `snippet/function/setoption/*.sublime-snippet` (setoption, blend, culling, antialias, billboard, shadow) to VS Code JSON entries in `vscode-aviutl-script/snippets/aviutl-script.code-snippets`
- [ ] T027 [P] [US2] Convert the 27 property snippets from `snippet/variable/*.sublime-snippet` (12 mutable: ox, oy, oz, zoom, alpha, aspect, rx, ry, rz, cx, cy, cz; 15 read-only: w, h, screen_w, screen_h, frame, framerate, time, totaltime, totalframe, index, num, layer, x, y, z) to VS Code JSON entries in `vscode-aviutl-script/snippets/aviutl-script.code-snippets` (note: `totalframeme.sublime-snippet` is a filename typo — content and trigger are `totalframe`)
- [ ] T028 [US2] Verify snippet count totals exactly 55 (1 + 21 + 6 + 27) in `vscode-aviutl-script/snippets/aviutl-script.code-snippets` and validate JSON syntax

**Checkpoint**: User Story 2 is complete — all 55 snippets are available in VS Code

---

## Phase 5: User Story 3 — Lua Language Features (Priority: P3)

**Goal**: Lua language editing features (comment toggling, bracket matching, auto-indentation) work correctly in AviUtl script files

**Independent Test**: Open an AviUtl script file, verify comment toggle inserts `--`, bracket auto-closing works, and indentation follows Lua conventions

### Implementation for User Story 3

- [ ] T029 [US3] Create `vscode-aviutl-script/language-configuration.json` with Lua language features per data-model.md and research R-005: line comment `--`, block comment `--[[ ]]`, brackets `(){}[]`, auto-closing pairs with `notIn` guards, surrounding pairs, indentation rules for Lua block keywords (increase: `function`, `if.*then`, `else`, `elseif.*then`, `for.*do`, `while.*do`, `repeat`; decrease: `end`, `else`, `elseif`, `until`)

**Checkpoint**: User Story 3 is complete — Lua editing features work in AviUtl script files

---

## Phase 6: Polish & Cross-Cutting Concerns

**Purpose**: Final validation and packaging

- [ ] T030 Run full `npm test` in `vscode-aviutl-script/` to verify all grammar tests still pass (regression check)
- [ ] T031 Verify `package.json` manifest completeness: all file paths in `contributes` point to existing files, `engines.vscode` is `^1.80.0`, all 5 file extensions registered, `firstLine` pattern set
- [ ] T032 Run quickstart.md validation: follow the setup and development workflow steps, verify `npm install`, `npm test`, and manual Extension Development Host (F5) all work

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies — can start immediately
- **Foundational (Phase 2)**: Depends on Setup completion — BLOCKS all user stories
- **User Story 1 (Phase 3)**: Depends on Foundational — this is the MVP
- **User Story 2 (Phase 4)**: Depends on Setup (Phase 1) only — can run in parallel with US1 (different files: snippets vs grammar)
- **User Story 3 (Phase 5)**: Depends on Setup (Phase 1) only — can run in parallel with US1 and US2 (different file: language-configuration.json)
- **Polish (Phase 6)**: Depends on ALL user stories being complete

### User Story Dependencies

- **User Story 1 (P1)**: Depends on Foundational (Phase 2) for grammar skeleton and test infrastructure — no dependencies on other stories
- **User Story 2 (P2)**: Depends on Phase 1 only (needs `package.json` with snippet contribution registered) — no dependencies on US1 or US3
- **User Story 3 (P3)**: Depends on Phase 1 only (needs `package.json` with language configuration registered) — no dependencies on US1 or US2

### Within User Story 1 (TDD flow)

- Test assertions (T008–T012) can be written in parallel [P] — all go in the same test file but target different patterns
- T013 (Red phase verification) depends on ALL test tasks (T008–T012)
- Implementation tasks (T014–T023) are sequential — each pattern is added and verified in a Red-Green cycle
- T023 (final Green verification) depends on all implementation tasks

### Parallel Opportunities

- T008, T009, T010, T011, T012 can run in parallel (all write different test sections)
- T024, T025, T026, T027 can run in parallel (all write different snippet categories to the same file — can be merged)
- T004 can run in parallel with T005, T006 (different files)
- US2 (Phase 4) and US3 (Phase 5) can start as soon as Phase 1 completes, in parallel with US1 (Phase 3)

---

## Parallel Example: User Story 1 (TDD)

```bash
# Launch all test assertion tasks in parallel:
Task: T008 "Write section label test assertions in test/aviutl-script.test.aviutl-script"
Task: T009 "Write global function test assertions in test/aviutl-script.test.aviutl-script"
Task: T010 "Write mutable property test assertions in test/aviutl-script.test.aviutl-script"
Task: T011 "Write read-only property test assertions in test/aviutl-script.test.aviutl-script"
Task: T012 "Write method test assertions in test/aviutl-script.test.aviutl-script"

# Then verify Red phase:
Task: T013 "Run npm test — all assertions FAIL"

# Then sequential Green implementation:
Task: T014 → T015 → T016 → T017 → T018 → T019 → T020 → T021 → T022 → T023
```

## Parallel Example: User Story 2 (Snippet Conversion)

```bash
# Launch all snippet conversion tasks in parallel:
Task: T024 "Convert environment template snippet"
Task: T025 "Convert 21 method snippets"
Task: T026 "Convert 6 setoption variant snippets"
Task: T027 "Convert 27 property snippets"

# Then verify:
Task: T028 "Verify 55 total snippets and JSON validity"
```

---

## Implementation Strategy

### MVP First (User Story 1 Only)

1. Complete Phase 1: Setup
2. Complete Phase 2: Foundational (grammar skeleton + test infrastructure)
3. Complete Phase 3: User Story 1 (TDD grammar development)
4. **STOP and VALIDATE**: Run `npm test` — all grammar assertions pass; launch Extension Development Host (F5) — AviUtl tokens are highlighted
5. This is already a useful extension — syntax highlighting is the core value

### Incremental Delivery

1. Setup + Foundational → Test infrastructure ready
2. User Story 1 → Grammar highlighting works → **MVP deliverable**
3. User Story 2 → Snippets work → Enhanced productivity
4. User Story 3 → Language config works → Complete editing experience
5. Polish → All validated → Ready for distribution

### Parallel Execution (Fastest Path)

1. Phase 1: Setup (sequential)
2. Phase 2: Foundational (sequential, depends on Phase 1)
3. In parallel:
   - Phase 3: User Story 1 (grammar TDD — depends on Phase 2)
   - Phase 4: User Story 2 (snippet conversion — depends on Phase 1 only)
   - Phase 5: User Story 3 (language config — depends on Phase 1 only)
4. Phase 6: Polish (after all stories complete)

---

## Notes

- [P] tasks = different files, no dependencies
- [Story] label maps task to specific user story for traceability
- Each user story is independently completable and testable
- TDD is mandatory (Constitution Principle II): write test assertions FIRST, verify FAIL, then implement
- **Git commit after EVERY task**: Run `git add` + `git commit` immediately after completing each task (T001, T002, …). Do NOT batch multiple tasks into a single commit. Each task = one atomic commit.
- **Commit message format**: Follow [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/). The description after the type/scope MUST start with a capitalized verb (e.g., `feat: Enable`, `test: Assert`, `chore: Use`). Keep the subject line concise and imperative — explain *why* and do not just *what*. Examples:
  - `chore: Initialize VS Code extension directory`
  - `chore: Use "vscode-tmgrammar-test"`
  - `test: Assert section label grammar`
  - `feat: Enable section-label pattern grammar`
  - `feat(snippets): Enable method snippets`
- The `totalframeme.sublime-snippet` filename typo (spec FR-009 note) should be converted as `totalframe` in the VS Code snippets
- Stop at any checkpoint to validate story independently
