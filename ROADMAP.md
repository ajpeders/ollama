# Roadmap

## Done
- 2026-09-20: back to Vulkan from ROCm (+50% gen t/s); repo created; benchmark harness.
- 2026-09-20: opencode explore/scout moved off qwen3:8b-32k onto the coder (dotfiles f678cca). They were evicting the coder, 5 swaps in 12 min. Only `debug` and `claude-local` (qwen3.6:27b) still cause swaps.

- 2026-09-20: can't avoid the 27B swap. Coder (18 GB) + qwen3.6:27b (17 GB) weights alone exceed 32 GB. Re-tested the coder under Claude Code: tool calls are fine now, but it guesses wrong absolute paths 3/3 runs, so `claude-local`/`debug` stay on the 27B and the swap is accepted.

## Next
1. Prompt-cache reuse: check that the coding clients (via llm-router) keep a stable prefix so prefill (~2.3 s at 8.6k tokens) gets skipped.
2. Track custom Modelfiles (e.g. `qwen3:8b-32k`) in `models/`.
