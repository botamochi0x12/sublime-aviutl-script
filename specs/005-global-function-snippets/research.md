# Research: Global Function Snippets

## Decision: Function Signatures

**Decision**: Use the signatures documented in the AviUtl scripting reference.
**Rationale**: These functions are already defined in the syntax grammar (`AviUtl.sublime-syntax` line 28, `aviutl-script.tmLanguage.json` line 22). Their signatures are well-established.
**Alternatives considered**: None — the AviUtl reference is the single authoritative source.

| Function | Signature | Arguments |
| -------- | --------- | --------- |
| `OR` | `OR(a, b)` | Two numeric operands |
| `AND` | `AND(a, b)` | Two numeric operands |
| `XOR` | `XOR(a, b)` | Two numeric operands |
| `SHIFT` | `SHIFT(a, b)` | Value and shift amount |
| `debug_print` | `debug_print(str)` | String to display |

## Decision: Snippet File Organization

**Decision**: Place global function snippets in `sublime-aviutl-script/snippet/function/` alongside existing `obj.*` method snippets. Name each file after the function (e.g., `OR.sublime-snippet`).
**Rationale**: The existing `snippet/function/` directory contains all callable function snippets. Global functions are conceptually similar to `obj.*` methods — they are callable functions the user invokes. Keeping them in the same directory maintains discoverability and follows the established convention.
**Alternatives considered**: Creating a separate `snippet/function/global/` subdirectory — rejected as unnecessary complexity for 5 files (violates Principle I: Simplicity).

## Decision: Japanese Descriptions

**Decision**: Use descriptions as provided in the user's feature request, which match the AviUtl scripting reference.
**Rationale**: Consistency with existing snippets that all use Japanese descriptions from the same reference.

| Function | Description |
| -------- | ----------- |
| `OR` | OR,AND,XORのビット演算をします。 |
| `AND` | OR,AND,XORのビット演算をします。 |
| `XOR` | OR,AND,XORのビット演算をします。 |
| `SHIFT` | 算術シフトをします。 |
| `debug_print` | デバック用の表示に使用します。 |

## Decision: No Grammar Changes Needed

**Decision**: No changes to `AviUtl.sublime-syntax` or `aviutl-script.tmLanguage.json`.
**Rationale**: All 5 functions are already matched by the existing grammar pattern `\b(OR|AND|XOR|RGB|HSV|SHIFT|debug_print)\b` and scoped as `support.function.library.aviutl-script`. Only snippets (autocompletion templates) are missing.
