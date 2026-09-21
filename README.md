# ollama

Config and benchmarks for the Ollama server on ArchDesktop (Radeon AI PRO R9700, 32 GB).
It serves the homelab via llm-router, Open WebUI and `ollama.thelunadog.com`.

## Quick start

```sh
bin/bench                      # benchmark qwen3-coder:30b against the live server
bin/apply                      # push systemd/override.conf to /etc, restart, verify backend
```

## Files

- `systemd/override.conf`: source of truth for the `ollama.service` drop-in
- `bin/apply`: diff, back up, install, restart, and print which GPU backend loaded
- `bin/bench`: repeatable speed benchmark (see [bench/results.md](bench/results.md))
- `bin/bench-depth`: prefill/generation at ~26k and ~52k tokens (agent depths)
- `bin/bench-cache`: 4 interleaved conversations; shows whether the prompt cache holds them

Secrets (`/etc/ollama/cloud.env`) are referenced but never stored here.
