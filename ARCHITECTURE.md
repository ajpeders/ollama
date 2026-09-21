# Architecture

```
clients (opencode, claude-local, Open WebUI, homelab apps) → ollama.service :11434 (Traefik for *.thelunadog.com) → Vulkan → R9700
```

- **Stock `ollama.service`** from the Arch package plus one drop-in, `override.conf`. The repo copy is canonical and `bin/apply` installs it.
- **Backend: Vulkan, not ROCm.** It's faster at generation on gfx1201 (+50% short, +22–30% at 26–52k depth) and slightly faster at prefill at depth (see bench/results.md). Both backend libs ship with Ollama, and it prefers ROCm when ROCm is visible, so `ROCR_VISIBLE_DEVICES=-1` is required alongside `OLLAMA_VULKAN=1`.
- **One pinned model:** `KEEP_ALIVE=-1`, `NUM_PARALLEL=1`, 98k context. qwen3-coder:30b takes 28 GB, so `MAX_LOADED_MODELS=2` can't actually hold a second model yet.
- **KV cache f16:** q8_0 is free at long context on Vulkan but costs about 7% on short prompts. Switch to it if VRAM matters more.
- **Prompt cache 12 GiB (`LLAMA_ARG_CACHE_RAM`).** One GPU slot holds the active conversation, and llama-server parks the others in host RAM. At the 8 GiB default, 3+ concurrent agent conversations thrash to 0% hits (10–30 s re-prefill per turn). 12 GiB holds ~4 at 26k and still leaves ~10 GB of the 30 GB RAM free.
