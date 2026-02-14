# Feature Specification: Compose VS Code Extension

**Feature Branch**: `004-vscode-extension-compose`
**Created**: 2026-02-14
**Status**: Draft
**Input**: User description: "Compose @vscode-aviutl-script/ as a VSCode extension"

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Install Extension from VSIX Package (Priority: P1)

An AviUtl scripter wants to install the AviUtl Script extension into VS Code so they can write `.obj`, `.anm`, `.scn`, `.cam`, and `.aviutl-script` files with syntax highlighting and code completion.

**Why this priority**: Without a packaged extension, none of the syntax highlighting or snippet features are usable in VS Code. This is the foundational delivery mechanism.

**Independent Test**: Can be fully tested by packaging the extension into a `.vsix` file, installing it in VS Code, and verifying that AviUtl Script files receive syntax highlighting and snippets are available.

**Acceptance Scenarios**:

1. **Given** the extension source in `vscode-aviutl-script/`, **When** a contributor runs the packaging command, **Then** a `.vsix` file is produced without errors
2. **Given** the `.vsix` file, **When** a user installs it in VS Code, **Then** the extension appears in the Extensions panel with correct name, description, and version
3. **Given** the extension is installed, **When** the user opens a `.obj` file, **Then** VS Code recognizes it as "AviUtl Script (Lua)" and applies syntax highlighting

---

### User Story 2 - Discover Extension on VS Code Marketplace (Priority: P2)

An AviUtl scripter searches for "AviUtl" in the VS Code Marketplace and finds the extension with a clear description, appropriate categorization, and identifying metadata (icon, publisher, repository link).

**Why this priority**: Marketplace discoverability is how most users find and install VS Code extensions. Without proper metadata, the extension is effectively invisible to potential users.

**Independent Test**: Can be tested by verifying all required Marketplace metadata fields are present in `package.json` and that the extension description and README communicate the extension's purpose to a Japanese-speaking AviUtl community.

**Acceptance Scenarios**:

1. **Given** the extension package, **When** it is submitted to the VS Code Marketplace, **Then** it passes all Marketplace validation checks
2. **Given** the Marketplace listing is live, **When** a user searches for "AviUtl", **Then** the extension appears in results with name, description, and icon
3. **Given** the Marketplace listing, **When** a user views the extension detail page, **Then** they see a README explaining supported file types, features (syntax highlighting, snippets), and a link to the source repository

---

### User Story 3 - Validate Extension Quality via Automated Tests (Priority: P3)

A contributor wants to verify that the extension's grammar and snippets work correctly before packaging, so they can catch regressions early.

**Why this priority**: Automated tests ensure ongoing quality as the extension evolves. Grammar tests already exist and pass; this story ensures the test workflow is documented and integrated into the packaging/CI pipeline.

**Independent Test**: Can be tested by running the existing test command and verifying all grammar assertions pass.

**Acceptance Scenarios**:

1. **Given** the extension source, **When** a contributor runs the test command, **Then** all grammar test assertions pass
2. **Given** a change to the grammar file, **When** tests are run, **Then** regressions are detected by failing assertions

---

### Edge Cases

- What happens when a user has the Lua extension installed alongside this extension? (The grammar inherits from `source.lua`, so both should coexist without conflict)
- What happens when a user opens a file with no extension but with the `-- AviUtl Script --` first-line marker? (The `firstLine` regex in `package.json` should detect it)
- What happens if the user has an older VS Code version below 1.80.0? (The `engines.vscode` field enforces the minimum version; installation should be blocked with a clear message)

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: Extension MUST be packageable into a `.vsix` file using standard VS Code extension tooling
- **FR-002**: Extension MUST include all required Marketplace metadata: publisher ID, display name, description, version, categories, repository URL, and license
- **FR-003**: Extension MUST include a README file that describes supported file types (`.obj`, `.anm`, `.scn`, `.cam`, `.aviutl-script`), features (syntax highlighting, 55 snippets), and usage instructions
- **FR-004**: Extension MUST include an icon image that visually identifies the extension in the Marketplace and Extensions panel
- **FR-005**: Extension MUST include a CHANGELOG file to communicate version history to users
- **FR-006**: Extension MUST declare the minimum VS Code version (currently `^1.80.0`) so incompatible installations are prevented
- **FR-007**: Extension MUST specify appropriate keywords (e.g., "AviUtl", "Lua", "video editing", "scripting") for Marketplace search discoverability
- **FR-008**: Extension MUST exclude development-only files (tests, test grammars, `node_modules`) from the packaged `.vsix` via a `.vscodeignore` file
- **FR-009**: Extension MUST pass all existing grammar tests before packaging
- **FR-010**: Extension MUST include a license file declaring the project's open-source license

### Key Entities

- **Extension Manifest** (`package.json`): Central configuration declaring language contributions, metadata, and Marketplace information
- **Extension Package** (`.vsix`): The distributable artifact containing grammar, snippets, language config, and metadata
- **Marketplace Listing**: The public-facing page where users discover and install the extension

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: Extension can be packaged from source into a `.vsix` file in a single command without errors
- **SC-002**: Extension passes all VS Code Marketplace validation checks when submitted
- **SC-003**: All existing grammar tests pass before and after packaging
- **SC-004**: Extension is discoverable by searching "AviUtl" in the VS Code Marketplace
- **SC-005**: Users can install the extension and immediately use syntax highlighting on supported file types without additional configuration
- **SC-006**: The packaged `.vsix` file excludes all development-only files, keeping the package size minimal

## Assumptions

- The existing grammar (`aviutl-script.tmLanguage.json`), snippets (`aviutl-script.code-snippets`), and language configuration (`language-configuration.json`) are complete and correct — this feature focuses on composition and packaging, not content changes
- A VS Code Marketplace publisher account will be available when the extension is ready to publish (account creation is outside this feature's scope)
- The extension targets VS Code version 1.80.0 and above, as already specified in `package.json`
- The primary audience is the Japanese AviUtl community, so descriptions should be bilingual (Japanese primary, English secondary) or Japanese-focused
- The project will use an MIT or similar permissive open-source license (consistent with being a public GitHub repository)
