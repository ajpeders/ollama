# Roadmap

## Done
- 2026-09-20: back to Vulkan from ROCm (+50% gen t/s); repo created; benchmark harness.
- 2026-09-20: opencode explore/scout moved off qwen3:8b-32k onto the coder (dotfiles f678cca). They were evicting the coder, 5 swaps in 12 min. Only `debug` and `claude-local` (qwen3.6:27b) still cause swaps.
- 2026-09-20: can't avoid the 27B swap. Coder (18 GB) + qwen3.6:27b (17 GB) weights alone exceed 32 GB. Re-tested the coder under Claude Code: tool calls are fine now, but it guesses wrong absolute paths 3/3 runs, so `claude-local`/`debug` stay on the 27B and the swap is accepted.
- 2026-09-20: prompt cache raised 8 → 12 GiB; 4-conversation follow-ups went from 10.5 s to 0 s. Vulkan re-confirmed at 26k/52k depth.
- 2026-09-22: 12 GiB prompt cache holds up on real traffic: 74% of prompt tokens came from cache over ~2 days, so no need for 16 GiB.
- 2026-09-22: KV cache f16 → q8_0 (−7% short gen, 0% long) plus `qwen2.5:7b-instruct` capped at 16k ctx via `models/`. The coder and carSearch/gym-app's qwen2.5 now fit in VRAM together; carSearch's 6-hourly refresh caused 4 of 6 coder evictions. Lowering the coder's num_batch was benchmarked (−9% / −22% prefill at 1024 / 512) and not needed.

## Next
1. Find the local `::1` client that still loads `qwen3:8b` (3 loads 09-21). Coder + qwen3:8b at 32k would be ~30 GiB, too tight.
2. Track the remaining custom Modelfiles (e.g. `qwen3:8b-32k`) in `models/`.
