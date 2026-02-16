# Feature Specification: Global Function Snippets

**Feature Branch**: `005-global-function-snippets`
**Created**: 2026-02-15
**Status**: Draft
**Input**: User description: "Add snippets for several functions: OR, AND, XOR (ビット演算), SHIFT (算術シフト), debug_print (デバック用の表示)"

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Bitwise Operation Snippet Insertion (Priority: P1)

An AviUtl script author needs to perform bitwise operations (OR, AND, XOR) on values. They type the function name as a trigger word and use tab-completion to insert the snippet with placeholders for both arguments.

**Why this priority**: Bitwise operations (OR, AND, XOR) share the same two-argument signature `(a, b)` and are the most commonly used global functions among the requested set. Delivering all three together provides immediate value.

**Independent Test**: Can be fully tested by typing `OR`, `AND`, or `XOR` in an AviUtl script file and triggering autocomplete. Each snippet expands to the function call with tab-stop placeholders for arguments.

**Acceptance Scenarios**:

1. **Given** an AviUtl script file is open in the editor, **When** the user types `OR` and triggers snippet expansion, **Then** the editor inserts `OR(a, b)` with `a` and `b` as tab-stop placeholders.
2. **Given** an AviUtl script file is open in the editor, **When** the user types `AND` and triggers snippet expansion, **Then** the editor inserts `AND(a, b)` with `a` and `b` as tab-stop placeholders.
3. **Given** an AviUtl script file is open in the editor, **When** the user types `XOR` and triggers snippet expansion, **Then** the editor inserts `XOR(a, b)` with `a` and `b` as tab-stop placeholders.

---

### User Story 2 - Arithmetic Shift Snippet Insertion (Priority: P1)

An AviUtl script author needs to perform arithmetic bit shifting. They type `SHIFT` and use tab-completion to insert the snippet with placeholders for both arguments.

**Why this priority**: SHIFT is a core bitwise utility function with the same two-argument signature. It completes the set of arithmetic/bitwise global functions.

**Independent Test**: Can be fully tested by typing `SHIFT` in an AviUtl script file and triggering autocomplete. The snippet expands to the function call with tab-stop placeholders.

**Acceptance Scenarios**:

1. **Given** an AviUtl script file is open in the editor, **When** the user types `SHIFT` and triggers snippet expansion, **Then** the editor inserts `SHIFT(a, b)` with `a` and `b` as tab-stop placeholders.

---

### User Story 3 - Debug Print Snippet Insertion (Priority: P1)

An AviUtl script author wants to output debug information. They type `debug_print` and use tab-completion to insert the snippet with a placeholder for the value to print.

**Why this priority**: Debug output is essential for script development workflow. It takes a single string argument and completes the set of requested global function snippets.

**Independent Test**: Can be fully tested by typing `debug_print` in an AviUtl script file and triggering autocomplete. The snippet expands to the function call with a tab-stop placeholder.

**Acceptance Scenarios**:

1. **Given** an AviUtl script file is open in the editor, **When** the user types `debug_print` and triggers snippet expansion, **Then** the editor inserts `debug_print(str)` with `str` as a tab-stop placeholder.

---

### Edge Cases

- What happens when a user types `or` (lowercase) — should it still trigger the `OR` snippet? (No — the trigger must match the exact function name casing, consistent with how the syntax grammar recognizes these as uppercase-only keywords.)
- What happens when the snippet is triggered outside an AviUtl script context (e.g., plain Lua)? (The snippets are scoped to `source.lua.aviutl-script` only and will not appear in non-AviUtl files.)

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: The package MUST provide a snippet for each global function: `OR`, `AND`, `XOR`, `SHIFT`, and `debug_print`.
- **FR-002**: Each snippet MUST use the exact function name (case-sensitive) as its tab trigger.
- **FR-003**: Each snippet MUST be scoped to `source.lua.aviutl-script` so it only activates in AviUtl script files.
- **FR-004**: The `OR`, `AND`, `XOR`, and `SHIFT` snippets MUST expand to `FUNC(${1:a},${2:b})$0` format with two tab-stop placeholders.
- **FR-005**: The `debug_print` snippet MUST expand to `debug_print(${1:str})$0` format with one tab-stop placeholder.
- **FR-006**: Snippets MUST be provided for both Sublime Text (`.sublime-snippet` files) and VS Code (`aviutl-script.code-snippets` entries).
- **FR-007**: Each snippet MUST include a Japanese description matching the function's purpose as documented in the AviUtl scripting reference.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: All five function snippets (OR, AND, XOR, SHIFT, debug_print) are available and expand correctly when triggered in an AviUtl script file.
- **SC-002**: Snippet expansion completes in a single trigger action (type name + tab) with no additional steps required.
- **SC-003**: Tab stops allow the user to fill in all required arguments sequentially without manual cursor repositioning.
- **SC-004**: Snippets do not appear or interfere when editing non-AviUtl script files.

## Assumptions

- The function signatures are based on the AviUtl scripting reference: OR/AND/XOR/SHIFT each take two numeric arguments; debug_print takes a single string argument.
- The existing snippet organization pattern is followed: global function snippets are placed alongside `obj.*` method snippets in the `snippet/function/` directory (Sublime) and in the shared `aviutl-script.code-snippets` file (VS Code).
- Snippet trigger names match the exact function names (uppercase for OR/AND/XOR/SHIFT, lowercase for debug_print) — consistent with how the syntax grammar recognizes them.
