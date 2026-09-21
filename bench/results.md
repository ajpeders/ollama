# Benchmark results

Host: ArchDesktop, Radeon AI PRO R9700 (32 GB, gfx1201), Ollama 0.34.2.
Model: `qwen3-coder:30b`, 98k context, flash attention on. Generated with `bin/bench`.

## 2026-09-20 — backend × KV cache type

| Config | Gen (short) | Gen (8.6k prompt) | Prefill |
|---|---|---|---|
| ROCm 7.2, KV f16 | 111 t/s | 95 t/s | ~3.8k t/s |
| ROCm 7.2, KV q8_0 | 101 t/s | 79 t/s | ~3.9k t/s |
| **Vulkan (RADV), KV f16** ← live | **169 t/s** | **134 t/s** | ~3.7k t/s |
| Vulkan (RADV), KV q8_0 | 157 t/s | 134 t/s | ~3.7k t/s |

`num_batch` 512/1024/2048: no gain over the default. Changing it forces a model reload.

## 2026-09-20 — backend at agent depth (`bin/bench-depth`)

The 2026-09-19 server spec found ROCm prefill ~2x faster than Vulkan at 11–22k tokens. That no longer reproduces:
the old Vulkan numbers likely predate `num_batch 2048` being baked into the qwen3-coder tag.

| Depth | Backend | Prefill | Gen | TTFT |
|---|---|---|---|---|
| 26k | ROCm | 2,443 t/s | 74 t/s | 10.5 s |
| 26k | **Vulkan** | **2,535 t/s** | **97 t/s** | 10.1 s |
| 52k | ROCm | 1,521 t/s | 57 t/s | 33.9 s |
| 52k | **Vulkan** | **1,625 t/s** | **70 t/s** | 31.7 s |

## 2026-09-20 — prompt cache (`bin/bench-cache`)

llama-server keeps inactive conversations in a host-RAM prompt cache (default 8 GiB; Ollama has no knob,
but passes `LLAMA_ARG_CACHE_RAM` through). Test: 4 conversations of ~26k tokens (~2.4 GB KV each), then one follow-up each.

| Cache RAM | Follow-up prefill |
|---|---|
| 8 GiB (default) | 10.5 s each: all 4 reprocessed (round-robin over > capacity = 0% LRU hits) |
| **12 GiB** ← live | **0 s each** |

Real traffic before the change (3 days of logs): 220 of 502 coder requests over 2k tokens were full reprocesses,
~1.9 h of prefill in total. Two alternating conversations were already fine at 8 GiB.

## Fixture note (2026-09-21)

`bench/long-prompt.txt` is now public Python stdlib source (json, textwrap, shlex, bisect, heapq; PSF license),
8,684 tokens. It replaced a fixture of private project code throughout the history. Same-config check: 3,846 t/s prefill /
133 t/s gen vs 3,700–3,800 / 134 with the old fixture, so earlier numbers remain comparable.
