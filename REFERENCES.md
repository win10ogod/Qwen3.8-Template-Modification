# References

The stable template and archived experiments in this repository were informed by several upstream/reference implementations.

## Qwen

- Qwen3.8-27B model repository: https://huggingface.co/Qwen/Qwen3.8-27B
- Qwen organization: https://huggingface.co/Qwen

Qwen-family token/role conventions are the primary format reference for this work.

## froggeric / Qwen-Fixed-Chat-Templates

- https://huggingface.co/froggeric/Qwen-Fixed-Chat-Templates

This project was useful as a comparison point for aggressive compatibility-oriented template modification. One important design lesson retained here is that a chat template does not need to become a semantic tool-schema repair engine.

The froggeric template is **not vendored** in this repository.

## Kimi K3

A K3 template supplied during the investigation was used as a structural reference for interleaved reasoning and tool-history layout.

Its JSON parsing machinery was briefly ported into an experimental Qwen template and was later removed. K3 and Qwen use different tool wire formats, so copying that parser created unnecessary coupling.

The full K3 template is **not vendored** here.

## DeepSeek Harness (DSH)

- https://github.com/deepseek-ai/deepseek-harness

DSH was the runtime/framework used during the premature-stop investigation. The practical root cause in the tested environment was ultimately found in the `fount-memory` memory-injection layer rather than in the chat template itself.
