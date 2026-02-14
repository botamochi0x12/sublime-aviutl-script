<!--
Sync Impact Report
===========================
Version change: N/A → 1.0.0 (initial ratification)
Modified principles: N/A (first version)
Added sections:
  - Core Principles (5 principles)
  - AviUtl Keyword Accuracy
  - Development Workflow & Quality Gates
  - Governance
Removed sections: N/A
Templates requiring updates:
  - .specify/templates/plan-template.md ✅ no changes needed
    (Constitution Check section is already generic/placeholder-driven)
  - .specify/templates/spec-template.md ✅ no changes needed
    (No constitution-specific constraints embedded)
  - .specify/templates/tasks-template.md ✅ no changes needed
    (Task categorization is feature-driven, not principle-driven)
  - No command files found in .specify/templates/commands/
Follow-up TODOs: none
-->

# Sublime-AviUtl-Script Constitution

## Core Principles

### I. Simplicity (YAGNI)

Every change MUST solve a current, concrete problem.
No speculative abstractions, wrapper layers, or "just in case"
code. Three similar lines are preferred over a premature
abstraction. If a simpler alternative exists, it MUST be chosen
unless a measurable reason justifies the complexity.

**Rationale**: This is a small editor package, not a framework.
Complexity has no audience here — only correctness and clarity.

### II. Test-First (TDD)

All behavioral changes MUST follow the Red-Green-Refactor cycle:

1. Write a failing test (or syntax-test assertion) that captures
   the desired behavior.
2. Implement the minimum code to make the test pass.
3. Refactor while keeping tests green.

Skipping the "red" step is NOT permitted. Tests MUST fail before
implementation begins.

**Rationale**: t_wada TDD discipline. A test that has never
failed proves nothing.

### III. Syntax Fidelity

The TextMate / Sublime / VSCode grammar MUST produce scopes that
exactly match the AviUtl scripting language semantics:

- Mutable properties → `variable.other.aviutl-script`
- Read-only constants → `entity.name.constant.aviutl-script`
- Methods → `support.function.library.aviutl-script`
- Section labels → `entity.name.section.aviutl-script`
- Global functions → `support.function.aviutl-script`

Any scope change MUST be validated by syntax tests before merge.
No "close enough" scoping is acceptable.

**Rationale**: Incorrect scoping silently breaks themes and
user expectations. Fidelity is the product.

### IV. Backward Compatibility

The existing Sublime Text 3 package MUST remain functional
after any change. New editor targets (VSCode, etc.) are additive
— they MUST NOT alter, remove, or break existing Sublime files
(`AviUtl.sublime-syntax`, `snippet/`, syntax test file).

**Rationale**: Existing users depend on the Sublime package.
Migration to new editors MUST NOT impose cost on current users.

### V. Single Source of Truth for Keywords

The canonical list of AviUtl keywords, properties, methods, and
constants MUST be maintained in one authoritative location.
When multiple editor grammars exist, they MUST derive from or
reference this single source. Divergence between editors is a
defect.

**Rationale**: Duplicate keyword lists inevitably drift. A single
source eliminates an entire class of consistency bugs.

## AviUtl Keyword Accuracy

All keywords, object properties, methods, and constants included
in any grammar or snippet MUST match the official AviUtl scripting
reference (https://ch.nicovideo.jp/usunoro/blomaga/ar915424).

- Adding a keyword that does not exist in the reference is
  forbidden unless the keyword is verified in AviUtl source or
  a widely-used plugin (e.g., `rikky_module`).
- Removing a keyword that exists in the reference is forbidden
  unless the keyword has been officially deprecated.
- Scope assignments MUST reflect the semantic role of each keyword
  (property vs. constant vs. method) as defined in Principle III.

## Development Workflow & Quality Gates

### Branching

- Feature work MUST occur on a dedicated branch named
  `###-descriptive-name` (e.g., `002-vscode-extension-migration`).
- The `master` branch is the stable release line.

### Commit Conventions

- Commit messages SHOULD use the gitmoji or conventional-commits
  style already established in this repository.
- Each commit SHOULD be atomic — one logical change per commit.

### Quality Gates (pre-merge)

All of the following MUST pass before a branch is merged:

1. **Syntax tests pass** — Sublime Text syntax test assertions
   report zero failures.
2. **No scope regressions** — Any keyword previously scoped MUST
   retain its scope unless intentionally changed and documented.
3. **Keyword accuracy** — New or modified keywords MUST be
   verified against the reference (see section above).
4. **TDD evidence** — PR description or commit history MUST show
   failing tests introduced before implementation commits.

## Governance

This constitution is the highest-authority document for project
decisions. When a practice conflicts with this constitution, the
constitution prevails.

### Amendments

- Any contributor MAY propose an amendment via pull request.
- Amendments MUST include: the change, rationale, and a migration
  plan if existing work is affected.
- Version increments follow Semantic Versioning:
  - **MAJOR**: Principle removed or fundamentally redefined.
  - **MINOR**: New principle or section added, or materially
    expanded guidance.
  - **PATCH**: Wording clarifications, typo fixes, non-semantic
    refinements.

### Compliance

- All pull requests MUST be checked against this constitution
  before merge.
- Complexity that violates Principle I MUST be explicitly
  justified in the PR description.

**Version**: 1.0.0 | **Ratified**: 2026-02-13 | **Last Amended**: 2026-02-13
