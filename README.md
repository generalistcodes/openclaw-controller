# OpenClaw Controller 🦞

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

### Done

- [x] Start/stop/status for gateway (direct process or LaunchAgent).
- [x] macOS LaunchAgent support via `launchctl` (no dependency on `openclaw` CLI for start/stop/status).
- [x] Help and description (`help`, `-h`, `--help`).

### Schedule (turn on/off at set times)

Goal: automatically **turn on** the gateway at one time (e.g. 8:00) and **turn off** at another (e.g. 23:00) so you don’t have to remember.

- **macOS:** Add a `schedule on HH:MM off HH:MM` (or config) that creates two launchd plists (or one with two `StartCalendarInterval` entries) that run `openclaw-ctl start` and `openclaw-ctl stop` at the given times.
- **Linux:** Same idea with cron (e.g. `0 8 * * * openclaw-ctl start` and `0 23 * * * openclaw-ctl stop`) or systemd user timers.
- **Behavior:** Schedule is separate from “run now” — manual `start`/`stop` still work; the scheduler just runs the same commands at fixed times.
- Possible commands: `openclaw-ctl schedule 08:00 23:00` (on at 08:00, off at 23:00), or `schedule clear` to remove the schedule.

### Other controls we could add

| Control | What it would do |
|--------|-------------------|
| **`restart`** | Stop then start. Useful after changing OpenClaw config. |
| **`logs`** | Tail or show gateway log path (e.g. `~/.openclaw/logs/gateway.log`); optional `-f` to follow. |
| **`health`** | Quick check: can we reach the gateway (e.g. HTTP/WS to port 18789)? Report up/down. |
| **`enable` / `disable`** | Turn “start on login” on or off (e.g. enable/disable RunAtLoad for the LaunchAgent, or systemd user service enable/disable). Gateway stays installed but won’t auto-start after reboot. |
| **Schedule** | As above: start at time X, stop at time Y. |
| **Install script path** | `openclaw-ctl install` to copy the script to `~/bin` or `/usr/local/bin` so you can run `openclaw-ctl` from anywhere. |

Not planned for now: idle timeout (stop after N minutes of no traffic) — would need a small daemon or cron that checks “activity” and is more involved.

### Planned

- [ ] Linux systemd user service support (detect and use `systemctl --user start/stop` when present).
- [ ] Schedule: `schedule on HH:MM off HH:MM` and `schedule clear` (launchd on macOS, cron or systemd timers on Linux).
- [ ] Optional: `restart`, `logs`, `health`, `enable`/`disable` (see table above).
- [ ] Optional: install target (e.g. `openclaw-ctl install` → copy to `~/bin` or `/usr/local/bin`).

## License

MIT.
