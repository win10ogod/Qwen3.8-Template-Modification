# Version history

This document separates **baseline**, **superseded historical versions**, **known-broken experiments**, and the **current stable** template.

Line counts are from the archived files captured during the 2026-09-27 investigation.

## v0 — baseline

Path: `baseline/original-2026-09-27.jinja`

Status: **Baseline / reference**

Approx. size: 170 lines.

Characteristics:

- legacy `enable_thinking` switch exists;
- legacy `preserve_thinking` switch exists;
- default reasoning effort is `xhigh`;
- strict tool instruction includes `ONLY reply ... with NO suffix`;
- tool arguments are expected to behave as mappings and are expanded with `|items`.

This file is preserved as the starting point. It is not labeled broken as a whole; several assumptions simply did not match the DSH/OpenAI-compatible history shapes used later.

## v1 — initial cleanup

Path: `archive/historical/v1-initial-fix.jinja`

Status: **Historical / superseded**

Approx. size: 247 lines.

Changes:

- default reasoning effort moved to `medium`;
- `preserve_thinking` was removed;
- broader reasoning-field compatibility was added (`reasoning_content`, `thinking`, `reasoning`);
- string tool arguments could be preserved raw instead of being forced through mapping iteration;
- tool prompt was relaxed compared with the baseline.

Still present:

- a legacy `enable_thinking` path.

This version was replaced because the design goal changed to permanent thinking.

## v1.1 — permanent thinking

Path: `archive/historical/v1-permanent-thinking.jinja`

Status: **Historical / superseded**

Approx. size: 234 lines.

Changes from v1:

- removed the `enable_thinking` switch entirely;
- thinking became a permanent template behavior.

It was later superseded by interleaved-thinking support.

## v2 — early interleaved thinking

Path: `archive/broken/v2-interleaved-history-drop.jinja`

Status: **Broken / experimental**

Approx. size: 242 lines.

Added:

- `enable_interleaved_thinking`, default `true`.

Known problem:

- when interleaved thinking was disabled, the same switch could also suppress already-existing post-tool historical reasoning;
- that rewrote history instead of only changing the next generation prefix.

This violated the intended rule that previously generated reasoning should remain stable in history.

## v2.1 — K3-derived JSON/tool parser

Path: `archive/broken/v2.1-k3-json-parser-strict.jinja`

Status: **Broken / experimental**

Approx. size: 577 lines.

Added:

- a large K3-derived Jinja JSON parser (`jp_*` helpers);
- reconstruction of JSON-string tool arguments into Qwen XML parameters;
- stricter tool-flow text requiring tool calls immediately after thinking.

Why retired:

- semantic parsing/repair of tool arguments does not belong in the chat-template layer;
- the parser greatly enlarged the template and the failure surface;
- strict tool-flow wording created an unnecessary conflict when a model wanted to emit brief commentary before a tool call.

## v3 — stallfix with parser retained

Path: `archive/broken/v3-stallfix-tool-parser.jinja`

Status: **Broken / experimental**

Approx. size: 616 lines.

Improved:

- inline reasoning-effort control-tag pre-scan;
- tool-flow wording was loosened;
- historical reasoning preservation was corrected so the interleaved switch did not erase existing reasoning.

Still wrong:

- the custom K3-derived JSON/tool parser remained.

This version was retired specifically to restore the serializer boundary.

## v4 — stable no-tool-parser

Path: `stable/chat_template.jinja`

Status: **Stable / recommended**

Approx. size: 281 lines.

SHA-256:

```text
2b0c16fe1f9dfbdd61097d9e1ebbfbe11add372cc7c4646417d4a8cda7bd066a
```

Properties:

- thinking permanently enabled;
- no `enable_thinking` switch;
- no `preserve_thinking` / `preserve_reasoning` switch;
- historical reasoning is retained;
- `enable_interleaved_thinking` defaults to `true`;
- default reasoning effort is `medium`;
- inline think-control tags are scanned and removed from visible prompt text;
- mapping tool arguments are serialized as parameters;
- string tool arguments are preserved verbatim;
- no semantic JSON/tool parser exists.

The v4 template is kept as the stable engineering result, while the original automatic-stop root cause is documented separately as the repaired `fount-memory` issue.
