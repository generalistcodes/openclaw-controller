# OpenClaw Controller

A small utility to **start and stop the OpenClaw gateway on demand** so you can save energy without uninstalling or breaking OpenClaw.

## The problem

OpenClaw runs as a 24/7 local AI assistant (gateway on port 18789, often installed as a macOS LaunchAgent or Linux service). When you’re not using it, the process still runs and uses resources. I wanted to:

- **Save energy** by not running the gateway when I don’t need it.
- **Keep OpenClaw installed** and ready — no uninstall, no reinstall.
- **Turn it on anytime** with one command, and turn it off when done.

So I built this controller: it only toggles *when* the gateway runs (start/stop), not *whether* it’s installed.

## What this does

- **`start`** — Starts the OpenClaw gateway (on macOS with LaunchAgent: `launchctl load`).
- **`stop`** — Stops the gateway (on macOS: `launchctl unload`).
- **`status`** — Shows if the gateway is running (or if the LaunchAgent is loaded).
- **`help`** — Description and usage.

On macOS, if the gateway was installed as a LaunchAgent (`openclaw gateway install`), the script uses `launchctl` only — so it works even when the `openclaw` CLI has issues (e.g. network interface errors).

## Requirements

- OpenClaw installed. On the LaunchAgent path, `openclaw` on PATH is optional.
- macOS or Linux; LaunchAgent support is macOS-only.

## Usage

```bash
chmod +x openclaw-ctl   # once
./openclaw-ctl start    # start gateway (default port 18789)
./openclaw-ctl stop     # stop gateway
./openclaw-ctl status   # show status
./openclaw-ctl help     # description and options
```

## Options (environment)

- `OPENCLAW_PORT` — Gateway port (default: `18789`).
- `OPENCLAW_CMD` — OpenClaw binary name (default: `openclaw`).

Example: `OPENCLAW_PORT=18790 ./openclaw-ctl start`

## Roadmap

- [x] Start/stop/status for gateway (direct process or LaunchAgent).
- [x] macOS LaunchAgent support via `launchctl` (no dependency on `openclaw` CLI for start/stop/status).
- [x] Help and description (`help`, `-h`, `--help`).
- [ ] Linux systemd user service support (detect and use `systemctl --user start/stop` when present).
- [ ] Optional: schedule start/stop (e.g. stop at night, start in the morning) via cron or launchd.
- [ ] Optional: simple install target (e.g. copy `openclaw-ctl` to `~/bin` or `/usr/local/bin`).

## License

MIT.
