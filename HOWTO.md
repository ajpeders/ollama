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
