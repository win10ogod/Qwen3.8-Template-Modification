# Root-cause report: DSH automatic mid-task stopping

## Final status

**Resolved in the tested environment.**

The repeated automatic mid-task stopping that triggered this template investigation was ultimately traced to the **`fount-memory` memory-injection layer**.

After `fount-memory` was fixed, the same environment completed **several thousand agent steps without the previous automatic interruption behavior**.

That result materially changes the conclusion of the earlier investigation: the chat template was not the primary cause of the DSH automatic-stop problem.

## Original symptom

Long-running agent tasks repeatedly exhibited this pattern:

1. reasoning clearly planned a next tool action;
2. the assistant sometimes emitted a short transition such as "Now downloading...";
3. no tool call followed;
4. the provider-facing result appeared as a normal `stop`;
5. the turn ended and the user had to ask the agent to continue.

Earlier exported sessions contained many such interruptions.

## Why the template initially looked suspicious

Several facts made the template a plausible suspect at first:

- some Qwen-family agent loops can emit premature EOS around reasoning/tool boundaries;
- early experimental templates contained overly strict tool-flow instructions;
- one experimental interleaved-thinking implementation could rewrite historical reasoning;
- later experiments incorrectly moved tool-argument parsing into Jinja.

Those were legitimate template problems, but they did not explain the persistent DSH auto-stop behavior after the tool parser was removed.

## The decisive clue

DSH injected `fount-memory` context containing prior agent trajectories, including incomplete runs.

Some recalled traces ended around the same semantic transition where later runs stopped. That created a strong in-context imitation hazard: an incomplete historical trajectory can look like a valid demonstration of how an assistant turn ends.

After the `fount-memory` behavior was fixed, the automatic stopping disappeared across several thousand subsequent steps.

## Current conclusion

For the tested stack:

- **DSH automatic mid-task stopping:** root cause was `fount-memory` / memory injection.
- **Chat-template regressions:** real but independent issues, documented separately.
- **Stable template:** retained because it is cleaner and more predictable, not because it is required to fix the resolved `fount-memory` bug.

## Privacy

Raw DSH session exports are intentionally not published in this repository because they may contain user prompts, environment details, tool outputs, paths, and other session-specific data.
