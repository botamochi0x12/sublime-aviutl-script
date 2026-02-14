# Tasks: Compose VS Code Extension

**Input**: Design documents from `specs/004-vscode-extension-compose/`
**Prerequisites**: plan.md, spec.md, research.md, data-model.md, quickstart.md

**Tests**: Test tasks are included for packaging verification (Constitution Principle II: TDD). Grammar tests already exist and pass — no new grammar test tasks needed.

**Organization**: Tasks are grouped by user story to enable independent implementation and testing.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3)
- Include exact file paths in descriptions

## Path Conventions

All paths are relative to `vscode-aviutl-script/` within the repository root.

---

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Install packaging tooling and remove stale build artifacts

- [ ] T001 Install `@vscode/vsce` as devDependency by running `npm install --save-dev @vscode/vsce` in `vscode-aviutl-script/`
- [ ] T002 Remove stale pre-existing `vscode-aviutl-script/aviutl-script-0.1.0.vsix` build artifact

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Create files that ALL user stories depend on

**⚠️ CRITICAL**: No user story work can begin until this phase is complete

- [ ] T003 [P] Create `vscode-aviutl-script/LICENSE` with MIT License text (copyright holder: botamochi0x12, year: 2026)
- [ ] T004 [P] Create `vscode-aviutl-script/images/icon.png` — 256x256 PNG extension icon (simple design recognizable at small sizes, works on light and dark themes)

**Checkpoint**: Foundation ready — user story implementation can now begin

---

## Phase 3: User Story 1 — Install Extension from VSIX Package (Priority: P1) 🎯 MVP

**Goal**: Package the extension into a `.vsix` file that users can install locally with working syntax highlighting and snippets.

**Independent Test**: Run `npx @vscode/vsce package` in `vscode-aviutl-script/` — `.vsix` file is created without errors. Install it in VS Code and open a `.obj` file to confirm syntax highlighting.

### Implementation for User Story 1

- [ ] T005 [US1] Rewrite `vscode-aviutl-script/.vscodeignore` to exclude `test/**`, `node_modules/**`, `.vscode/**`, `*.vsix`, `.git/**`, `.github/**`, `.gitignore`, `package-lock.json` — but keep `README.md`, `CHANGELOG.md`, `LICENSE` included (per research.md R3)
- [ ] T006 [US1] Add `"vscode:prepublish": "npm test"` to `scripts` in `vscode-aviutl-script/package.json` so grammar tests run before every package/publish
- [ ] T007 [US1] Add `"license": "MIT"` field to `vscode-aviutl-script/package.json`
- [ ] T008 [US1] Verify packaging by running `npx @vscode/vsce ls` in `vscode-aviutl-script/` and confirm README.md, CHANGELOG.md, LICENSE, images/icon.png are included; test/, node_modules/, *.vsix are excluded
- [ ] T009 [US1] Run `npx @vscode/vsce package` in `vscode-aviutl-script/` and verify `.vsix` is produced without errors

**Checkpoint**: User Story 1 complete — extension is packageable and installable from `.vsix`

---

## Phase 4: User Story 2 — Discover Extension on VS Code Marketplace (Priority: P2)

**Goal**: Add all Marketplace metadata so the extension is discoverable and presentable when published.

**Independent Test**: Run `npx @vscode/vsce ls` to verify all metadata files are included. Inspect `package.json` for all required Marketplace fields. Verify README.md renders correctly.

### Implementation for User Story 2

- [ ] T010 [P] [US2] Create `vscode-aviutl-script/README.md` — bilingual (Japanese primary, English headers) description of supported file types (`.obj`, `.anm`, `.scn`, `.cam`, `.aviutl-script`), features (syntax highlighting, 55 snippets), snippet usage examples, VS Code version requirement, and license reference (per research.md R5)
- [ ] T011 [P] [US2] Create `vscode-aviutl-script/CHANGELOG.md` following Keep a Changelog format — initial entry for v0.1.0 with: syntax highlighting, 55 snippets, supported file extensions, language configuration (per research.md R7)
- [ ] T012 [US2] Add Marketplace metadata fields to `vscode-aviutl-script/package.json`: `publisher` (placeholder: `"botamochi0x12"`), `repository` (`{ "type": "git", "url": "https://github.com/botamochi0x12/sublime-aviutl-script" }`), `icon` (`"images/icon.png"`), `keywords` (`["AviUtl", "Lua", "aviutl-script", "video editing", "scripting"]`), `homepage` (`"https://github.com/botamochi0x12/sublime-aviutl-script"`) (per research.md R2)
- [ ] T013 [US2] Re-run `npx @vscode/vsce package` to verify the enriched package builds without errors and passes Marketplace validation

**Checkpoint**: User Story 2 complete — extension has full Marketplace metadata and documentation

---

## Phase 5: User Story 3 — Validate Extension Quality via Automated Tests (Priority: P3)

**Goal**: Ensure grammar tests are integrated into the packaging pipeline and documented for contributors.

**Independent Test**: Run `npm test` in `vscode-aviutl-script/` — all grammar assertions pass. Modify a scope in the grammar, run `npm test` again — test fails (regression detected).

### Implementation for User Story 3

- [ ] T014 [US3] Verify `vscode:prepublish` hook triggers `npm test` by running `npx @vscode/vsce package` and confirming grammar tests execute before packaging
- [ ] T015 [US3] Verify regression detection by temporarily breaking a scope in `vscode-aviutl-script/syntaxes/aviutl-script.tmLanguage.json`, running `npm test`, confirming failure, then reverting the change

**Checkpoint**: User Story 3 complete — automated test pipeline validated

---

## Phase 6: Polish & Cross-Cutting Concerns

**Purpose**: Final validation across all stories

- [ ] T016 Run full packaging verification: `npm test && npx @vscode/vsce ls && npx @vscode/vsce package` in `vscode-aviutl-script/`
- [ ] T017 Verify backward compatibility: confirm no changes to Sublime Text files (`AviUtl.sublime-syntax`, `snippet/`, `syntax-test_aviutl-script.aviutl-script`)

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies — start immediately
- **Foundational (Phase 2)**: Depends on Setup (T001 must complete for vsce to be available)
- **User Story 1 (Phase 3)**: Depends on Foundational (needs LICENSE, icon)
- **User Story 2 (Phase 4)**: Depends on User Story 1 (needs working .vscodeignore and license field)
- **User Story 3 (Phase 5)**: Depends on User Story 1 (needs vscode:prepublish hook)
- **Polish (Phase 6)**: Depends on all user stories complete

### User Story Dependencies

- **User Story 1 (P1)**: Can start after Foundational — no dependencies on other stories
- **User Story 2 (P2)**: Can start after US1 — adds metadata on top of working package
- **User Story 3 (P3)**: Can start after US1 — validates prepublish hook from US1

### Parallel Opportunities

- T003 and T004 (Phase 2) can run in parallel — different files
- T010 and T011 (Phase 4) can run in parallel — different files
- T005, T006, T007 (Phase 3) touch the same files (package.json, .vscodeignore) and should run sequentially

---

## Parallel Example: User Story 2

```bash
# Launch README and CHANGELOG creation together (different files):
Task: "Create README.md in vscode-aviutl-script/README.md"
Task: "Create CHANGELOG.md in vscode-aviutl-script/CHANGELOG.md"

# Then sequentially:
Task: "Add Marketplace metadata to package.json"
Task: "Re-run vsce package to validate"
```

---

## Implementation Strategy

### MVP First (User Story 1 Only)

1. Complete Phase 1: Setup (install vsce)
2. Complete Phase 2: Foundational (LICENSE + icon)
3. Complete Phase 3: User Story 1 (.vscodeignore, prepublish hook, license field, package)
4. **STOP and VALIDATE**: `npx @vscode/vsce package` produces a valid `.vsix`
5. Extension is installable and functional — MVP achieved

### Incremental Delivery

1. Setup + Foundational → tooling and shared assets ready
2. User Story 1 → packageable extension → **MVP!**
3. User Story 2 → Marketplace-ready metadata and docs
4. User Story 3 → validated test pipeline
5. Polish → final cross-cutting verification

---

## Notes

- [P] tasks = different files, no dependencies
- [Story] label maps task to specific user story for traceability
- Constitution Principle II (TDD): grammar tests already exist and pass; T014–T015 validate their integration into the packaging pipeline
- Constitution Principle IV (Backward Compatibility): T017 explicitly verifies no Sublime files were changed
- The `publisher` field in T012 uses a placeholder value — the repo owner must set the actual publisher ID before Marketplace publishing
