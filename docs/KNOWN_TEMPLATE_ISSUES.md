# Known template issues discovered during the investigation

These issues are about the **template implementations themselves**. They are separate from the resolved DSH `fount-memory` automatic-stop root cause.

## 1. Legacy thinking switches created too many state combinations

The baseline exposed both `enable_thinking` and `preserve_thinking`.

For the target usage, this created unnecessary state combinations and made history/generation behavior harder to reason about.

Stable policy:

- thinking is permanently enabled;
- historical reasoning is preserved;
- only interleaved post-tool thinking has its own switch.

## 2. Early interleaved switch rewrote history

The first interleaved implementation used the switch while rendering historical assistant messages.

Result: disabling interleaved thinking could suppress reasoning that had already been generated after tool results.

Stable policy:

- switches may affect the **next generation prefix**;
- they do not delete existing historical reasoning.

## 3. K3-derived tool-argument parser was the wrong abstraction

Experimental v2.1/v3 parsed JSON strings inside Jinja and reconstructed tool parameters.

That was removed because the template should serialize the data it receives, not become a schema-aware repair layer.

Stable policy:

```text
mapping arguments -> serialize as Qwen parameters
string arguments  -> preserve verbatim
```

No parse, repair, normalization, or semantic inference.

## 4. Overly strict tool-flow instructions

One experimental version effectively required:

- tool call immediately after thinking;
- no conversational text before/after it.

That can create a logical conflict if the model has already emitted a short commentary transition and then decides to call a tool.

Stable wording is intentionally less absolute.

## 5. Tool-schema validation errors are not a Jinja-repair problem

During testing, DSH reported a Todo schema mismatch involving missing `status` fields and undeclared `activeForm` fields.

The stable template does not attempt to "fix" such values. If a model emits arguments for the wrong schema, the tool/runtime should reject them and expose the error clearly.

## 6. Template optimization should prefer subtraction

A recurring lesson from this work:

> Fewer hidden transformations make failures easier to attribute.

The stable version therefore avoids parser-like behavior unless it is strictly required for Qwen message serialization.
