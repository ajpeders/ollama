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
