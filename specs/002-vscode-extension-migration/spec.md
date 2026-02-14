# Feature Specification: Migrate AviUtl Syntax Highlighting to VS Code Extension

**Feature Branch**: `002-vscode-extension-migration`
**Created**: 2026-02-09
**Status**: Draft
**Input**: User description: "Migrate AviUtl Sublime Syntax to VS Code Extension — create a new VS Code extension that provides equivalent syntax highlighting and code snippets for AviUtl scripting (Lua-based), converting the existing Sublime Text 3 package into VS Code format."

## User Scenarios & Testing *(mandatory)*

### User Story 1 - AviUtl Script Syntax Highlighting in VS Code (Priority: P1)

As an AviUtl script author using VS Code, I want my `.obj`, `.anm`, `.scn`, `.cam`, and `.aviutl-script` files to be recognized and syntax-highlighted so that I can read and write AviUtl scripts with the same visual clarity as in Sublime Text.

**Why this priority**: Syntax highlighting is the core value proposition of the extension. Without it, the extension has no purpose. This must work before anything else is useful.

**Independent Test**: Can be fully tested by opening any AviUtl script file in VS Code with the extension installed and verifying that Lua base syntax, AviUtl-specific keywords, object properties, methods, constants, and section labels are all highlighted with distinct colors.

**Acceptance Scenarios**:

1. **Given** VS Code with the extension installed, **When** a user opens a file with extension `.obj`, `.anm`, `.scn`, `.cam`, or `.aviutl-script`, **Then** the file is automatically recognized as "AviUtl Script (Lua)" and syntax highlighting is applied.
2. **Given** VS Code with the extension installed, **When** a user opens a file whose first line is `-- AviUtl Script --`, **Then** the file is recognized as "AviUtl Script (Lua)" regardless of its extension.
3. **Given** an open AviUtl script file, **When** the user types `obj.ox`, `obj.oy`, `obj.zoom`, or other mutable properties, **Then** `obj` is highlighted as a built-in constant and the property name is highlighted as a mutable variable.
4. **Given** an open AviUtl script file, **When** the user types `obj.w`, `obj.time`, `obj.frame`, or other read-only properties, **Then** the property name is highlighted as a read-only constant, distinct from mutable properties.
5. **Given** an open AviUtl script file, **When** the user types `obj.draw()`, `obj.load()`, `obj.mes()`, or other methods, **Then** the method name is highlighted as a library function.
6. **Given** an open AviUtl script file, **When** the user types `OR`, `AND`, `XOR`, `RGB`, `HSV`, `SHIFT`, or `debug_print`, **Then** these are highlighted as AviUtl library functions.
7. **Given** an open AviUtl script file, **When** the user types `@label_name` at the start of a line, **Then** the `@` symbol and the label name are highlighted as a section label.
8. **Given** an open AviUtl script file, **When** the user writes standard Lua syntax (comments, strings, keywords like `function`, `end`, `if`, `then`), **Then** these are highlighted correctly using the inherited Lua grammar.

---

### User Story 2 - AviUtl Code Snippets in VS Code (Priority: P2)

As an AviUtl script author, I want code snippets so that I can quickly insert commonly used AviUtl function calls, object properties, and boilerplate code using tab completion.

**Why this priority**: Snippets significantly improve productivity but the extension is still useful without them (syntax highlighting alone provides value). This is the second most important feature.

**Independent Test**: Can be fully tested by opening an AviUtl script file, typing a snippet trigger (e.g., `draw`, `ox`, `!env`), pressing Tab, and verifying the correct code is inserted with appropriate tab stops.

**Acceptance Scenarios**:

1. **Given** an open AviUtl script file, **When** the user types `draw` and triggers completion, **Then** the `obj.draw()` snippet is inserted with appropriate placeholders.
2. **Given** an open AviUtl script file, **When** the user types `ox` and triggers completion, **Then** the `obj.ox` property snippet is inserted.
3. **Given** an open AviUtl script file, **When** the user types `!env` and triggers completion, **Then** the AviUtl environment boilerplate template is inserted.
4. **Given** an open AviUtl script file, **When** a snippet is inserted, **Then** tab stops allow the user to navigate between placeholder fields in order.
5. **Given** VS Code with the extension installed, **When** the user views the snippet list for AviUtl Script files, **Then** all 55 snippets from the original Sublime package are available.

---

### User Story 3 - Lua Language Features in AviUtl Files (Priority: P3)

As an AviUtl script author, I want standard Lua language editing features (comment toggling, bracket matching, auto-indentation) to work correctly in my AviUtl script files.

**Why this priority**: These are quality-of-life features that rely on proper language configuration. The extension is functional without them, but they significantly improve the editing experience.

**Independent Test**: Can be fully tested by opening an AviUtl script file and verifying that pressing the comment shortcut toggles `--` line comments, typing `{` auto-inserts `}`, and indentation follows Lua conventions after `function`/`then`/`do` keywords.

**Acceptance Scenarios**:

1. **Given** an open AviUtl script file, **When** the user presses the comment toggle shortcut, **Then** `--` line comments are toggled correctly.
2. **Given** an open AviUtl script file, **When** the user selects a block and presses the block comment shortcut, **Then** `--[[ ]]` block comments are applied.
3. **Given** an open AviUtl script file, **When** the user types an opening bracket (`(`, `{`, `[`, `"`), **Then** the matching closing bracket is auto-inserted.
4. **Given** an open AviUtl script file, **When** the user presses Enter after a `function`, `then`, `do`, or `repeat` keyword, **Then** the next line is auto-indented.

---

### Edge Cases

- What happens when a file has an `.obj` extension but contains non-AviUtl content (e.g., a 3D model file)? The extension should still apply AviUtl highlighting by default for `.obj` files, since this is the same behavior as the Sublime package. Users can manually override the language mode.
- How does the extension behave when Lua extensions or other Lua-based syntax highlighters are also installed? The AviUtl grammar should take priority for registered file extensions, while inheriting from the base Lua grammar for standard Lua constructs.
- What happens when snippet triggers conflict with Lua keywords or other extension snippets? AviUtl snippets should only activate in files recognized as AviUtl Script (Lua), preventing conflicts in other file types.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: The extension MUST register file extensions `.obj`, `.anm`, `.scn`, `.cam`, and `.aviutl-script` as "AviUtl Script (Lua)" language.
- **FR-002**: The extension MUST recognize files whose first line matches `-- AviUtl Script --` as AviUtl Script (Lua).
- **FR-003**: The extension MUST highlight AviUtl global functions (`OR`, `AND`, `XOR`, `RGB`, `HSV`, `SHIFT`, `debug_print`) as library functions.
- **FR-004**: The extension MUST highlight `obj.` followed by mutable properties (`ox`, `oy`, `oz`, `zoom`, `alpha`, `aspect`, `rx`, `ry`, `rz`, `cx`, `cy`, `cz`) with the `obj` portion as a built-in constant and the property name as a mutable variable scope.
- **FR-005**: The extension MUST highlight `obj.` followed by read-only properties (`w`, `h`, `screen_w`, `screen_h`, `index`, `num`, `time`, `totaltime`, `frame`, `totalframe`, `framerate`, `x`, `y`, `z`, `layer`, `check0`, `track0`-`track3`, `color`, `dialog`, `file`, `param`) as read-only constants, visually distinct from mutable properties.
- **FR-006**: The extension MUST highlight `obj.` followed by methods (`mes`, `effect`, `rand`, `draw`, `drawpoly`, `load`, `setfont`, `filter`, `setanchor`, `interpolation`, `getaudio`, `setoption`, `getoption`, `getvalue`, `getinfo`, `copybuffer`, `getpixel`, `putpixel`, `copypixel`, `pixeloption`, `getpixeldata`, `putpixeldata`) as library function names.
- **FR-007**: The extension MUST highlight section labels (`@label_name` at the start of a line) with the `@` symbol as keyword punctuation and the label name as a label entity.
- **FR-008**: The extension MUST inherit all standard Lua syntax highlighting (comments, strings, numbers, keywords, operators) from the base Lua grammar.
- **FR-009**: The extension MUST provide all 55 snippets from the original Sublime Text package, converted to VS Code snippet format, including: 1 environment template (`!env`), 21 method snippets, 6 setoption variant snippets, and 27 property snippets (12 mutable + 15 read-only). Note: the source file `totalframeme.sublime-snippet` is a filename typo for `totalframe`.
- **FR-010**: The extension MUST provide Lua-appropriate language configuration: line comments (`--`), block comments (`--[[ ]]`), bracket pairs, auto-closing pairs, and indentation rules for Lua block keywords.
- **FR-011**: AviUtl-specific syntax rules MUST take priority over base Lua rules when both could match (AviUtl patterns are checked first).

### Key Entities

- **Grammar (TextMate)**: The syntax definition that maps AviUtl-specific patterns to scope names for syntax highlighting. Converted from Sublime's context-based `.sublime-syntax` format to TextMate's pattern-based `.tmLanguage.json` format.
- **Snippet**: A code template with a trigger word and tab-stop placeholders. Each of the 55 original Sublime XML snippets is converted to a JSON entry in a single VS Code `.code-snippets` file.
- **Language Configuration**: Settings that define comment styles, bracket pairs, auto-closing behavior, and indentation rules for the AviUtl Script language in VS Code.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: All 5 registered file extensions (`.obj`, `.anm`, `.scn`, `.cam`, `.aviutl-script`) are automatically recognized as AviUtl Script (Lua) when opened in VS Code.
- **SC-002**: 100% of AviUtl-specific syntax patterns from the original Sublime syntax (7 global functions, 12 mutable properties, 20+ read-only constants, 22+ methods, section labels) are highlighted correctly with distinct visual scopes.
- **SC-003**: All 55 code snippets are available and produce the same output as their Sublime Text equivalents when triggered.
- **SC-004**: Standard Lua syntax (comments, strings, numbers, keywords) highlights correctly in AviUtl script files, with no regressions from the base Lua grammar.
- **SC-005**: An AviUtl script author can install the extension and immediately begin editing script files with full syntax highlighting and snippet support, with no additional configuration required.

## Assumptions

- The extension will be created as a new directory (`vscode-aviutl-script/`) within the existing repository, keeping the Sublime package intact.
- The TextMate grammar format (`.tmLanguage.json`) is used, which is the standard for VS Code syntax highlighting and is well-documented.
- The `meta_scope: meta.block.aviutl-script` from the Sublime syntax cannot be replicated in TextMate grammars. Since this scope is not used for theming, it is safe to omit.
- Snippet tabstop syntax (`$1`, `${1:placeholder}`, `$0`) is largely compatible between Sublime and VS Code, with minor escaping differences (e.g., `\}` to `\\}` in JSON).
- The extension targets VS Code (not other editors) and uses standard VS Code extension APIs and packaging conventions.
- The scope name `source.lua.aviutl-script` is used to extend the base `source.lua` scope, maintaining compatibility with Lua-targeting themes.
