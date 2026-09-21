# Roadmap

## Done
- 2026-09-20: back to Vulkan from ROCm (+50% gen t/s); repo created; benchmark harness.
- 2026-09-20: opencode explore/scout moved off qwen3:8b-32k onto the coder (dotfiles f678cca). They were evicting the coder, 5 swaps in 12 min. Only `debug` and `claude-local` (qwen3.6:27b) still cause swaps.
- 2026-09-20: can't avoid the 27B swap. Coder (18 GB) + qwen3.6:27b (17 GB) weights alone exceed 32 GB. Re-tested the coder under Claude Code: tool calls are fine now, but it guesses wrong absolute paths 3/3 runs, so `claude-local`/`debug` stay on the 27B and the swap is accepted.
- 2026-09-20: prompt cache raised 8 → 12 GiB; 4-conversation follow-ups went from 10.5 s to 0 s. Vulkan re-confirmed at 26k/52k depth.

## Next
1. Watch real hit rate for a few days (re-run the log analysis in HOWTO). If RAM allows and misses persist with 5+ agents, try 16 GiB.
2. Track custom Modelfiles (e.g. `qwen3:8b-32k`) in `models/`.
