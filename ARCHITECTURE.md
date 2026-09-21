# Architecture

```
clients (≈15 projects, Open WebUI) → llm-router / Traefik → ollama.service :11434 → Vulkan → R9700
```

- **Stock `ollama.service`** from the Arch package plus one drop-in, `override.conf`. The repo copy is canonical and `bin/apply` installs it.
- **Backend: Vulkan, not ROCm.** It's about 50% faster at generation on gfx1201 (see bench/results.md). Both backend libs ship with Ollama, and it prefers ROCm when ROCm is visible, so `ROCR_VISIBLE_DEVICES=-1` is required alongside `OLLAMA_VULKAN=1`.
- **One pinned model:** `KEEP_ALIVE=-1`, `NUM_PARALLEL=1`, 98k context. qwen3-coder:30b takes 28 GB, so `MAX_LOADED_MODELS=2` can't actually hold a second model yet.
- **KV cache f16:** q8_0 is free at long context on Vulkan but costs about 7% on short prompts. Switch to it if VRAM matters more.
