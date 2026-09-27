# Qwen3.8 Template Modification

A versioned archive of Qwen 3.8-compatible chat-template experiments, regressions, and the current stable template.

## Current stable

**Use:** `stable/chat_template.jinja`

SHA-256:

```text
2b0c16fe1f9dfbdd61097d9e1ebbfbe11add372cc7c4646417d4a8cda7bd066a
```

The stable template keeps the useful changes from this investigation while deliberately avoiding semantic tool-argument parsing:

- thinking is permanently enabled;
- historical reasoning is preserved;
- `enable_interleaved_thinking` exists and defaults to `true`;
- reasoning effort defaults to `medium`;
- inline `<|think_xhigh|>`, `<|think_medium|>`, and `<|think_low|>`-style steering tags are consumed as control metadata instead of being left in the prompt;
- mapping tool arguments are serialized to Qwen's parameter format;
- string tool arguments are preserved verbatim;
- the template does **not** parse, repair, normalize, or infer tool-argument semantics.

## Root-cause update

The original investigation started because long DSH agent runs repeatedly stopped in the middle of work.

That automatic-stop problem was ultimately traced to the **`fount-memory` memory-injection layer**, not to the chat template. After `fount-memory` was fixed in the tested environment, the agent ran **several thousand steps without the previous automatic interruption**.

The template work still uncovered several independent template regressions, so those versions are kept here as an engineering archive.

See [docs/ROOT_CAUSE.md](docs/ROOT_CAUSE.md).

## Version map

| Status | Version | Path | Notes |
|---|---|---|---|
| **Stable** | v4 | `stable/chat_template.jinja` | Current recommended version. No custom tool parser. |
| Baseline | v0 | `baseline/original-2026-09-27.jinja` | Original 171-line template used as the starting point. |
| Historical | v1 | `archive/historical/v1-initial-fix.jinja` | First broad cleanup; later superseded. |
| Historical | v1.1 | `archive/historical/v1-permanent-thinking.jinja` | Removed the thinking-off path; later superseded. |
| **Broken / experimental** | v2 | `archive/broken/v2-interleaved-history-drop.jinja` | Early interleaved-thinking implementation could omit existing post-tool reasoning when disabled. |
| **Broken / experimental** | v2.1 | `archive/broken/v2.1-k3-json-parser-strict.jinja` | Added a K3-derived JSON/tool parser plus stricter tool-flow logic. |
| **Broken / experimental** | v3 | `archive/broken/v3-stallfix-tool-parser.jinja` | Improved control-tag/tool-flow behavior, but still retained the unnecessary custom JSON parser. |

Detailed history: [docs/VERSION_HISTORY.md](docs/VERSION_HISTORY.md).

## What is intentionally not vendored

Two external templates were used as references during the investigation:

- [froggeric/Qwen-Fixed-Chat-Templates](https://huggingface.co/froggeric/Qwen-Fixed-Chat-Templates)
- the Kimi K3 template supplied during testing

Their full templates are not republished here. See [REFERENCES.md](REFERENCES.md).

## Tests

A small regression suite lives in `tests/test_template.py`.

It checks the invariants that matter most for the stable version:

- Jinja renders successfully;
- interleaved thinking defaults on;
- thinking cannot be disabled through the removed legacy switch;
- mapping arguments serialize normally;
- raw string arguments remain raw;
- no K3-derived `jp_*` JSON parser helpers remain.

## Scope

This repository is an engineering archive for the chat-template layer. It should not be read as a claim that every model/provider/framework premature-stop issue is solvable in Jinja.

## License

Apache-2.0. See [LICENSE](LICENSE).
