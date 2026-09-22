# How-to

## Change a server setting
1. Edit `systemd/override.conf`.
2. `bin/bench <model> before` (optional baseline)
3. `bin/apply`: shows the diff, backs up `/etc/...override.conf.bak-<timestamp>`, restarts.
4. `bin/bench <model> after`, then add the numbers to `bench/results.md`.
5. Commit.

## Check which GPU backend is live
```sh
journalctl -u ollama --since -10min | grep 'using device'
```
You should see `Vulkan0 (AMD Radeon AI PRO R9700 ...)`. If it says `ROCm0`, `ROCR_VISIBLE_DEVICES=-1` is missing.

## Roll back
```sh
ls /etc/systemd/system/ollama.service.d/          # pick a .bak-* file
sudo cp <backup> /etc/systemd/system/ollama.service.d/override.conf
sudo systemctl daemon-reload && sudo systemctl restart ollama
```

## Check prompt-cache hit rate from real traffic
`bin/report -3d` gives the summary (plus evictions and prompt sizes). To dig into single requests:
Each request logs its prompt size and how many tokens were actually processed. Their difference is what came from the cache:
```sh
journalctl -u ollama --since -1d -o cat | grep -E 'task\.n_tokens|prompt eval time' | less
# "task.n_tokens = N" then "prompt eval time = … / M tokens": M ≈ N means a miss, M ≪ N means a hit
```

## Rebuild a customized model tag
`ollama pull` resets a tag to the library version and drops local parameters. Afterwards, re-apply the local ones:
```sh
for f in models/*.Modelfile; do sh -c "$(sed -n 's/^# Rebuild: //p' "$f")"; done
```
Don't test a Modelfile by building it under a temp name and running `ollama rm` on it while the real model is loaded. They share a blob, so the rm unloads the live model too.
