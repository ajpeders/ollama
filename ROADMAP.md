# Roadmap

## Done
- 2026-09-20: back to Vulkan from ROCm (+50% gen t/s); repo created; benchmark harness.
- 2026-09-20: opencode explore/scout moved off qwen3:8b-32k onto the coder (dotfiles f678cca). They were evicting the coder, 5 swaps in 12 min. Only `debug` and `claude-local` (qwen3.6:27b) still cause swaps.

## Next
1. Prompt-cache reuse: check that the coding clients (via llm-router) keep a stable prefix so prefill (~2.3 s at 8.6k tokens) gets skipped.
2. Two-model residency: KV q8_0 and per-model num_ctx, so the coder and a small model stop evicting each other.
3. Track custom Modelfiles (e.g. `qwen3:8b-32k`) in `models/`.
