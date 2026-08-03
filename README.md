# Tuxility

A small set of maintenance tools for Fedora Atomic desktops (Silverblue, Kinoite, etc.).

## System Update

`system-update` is a one-command updater for OSTree-based Fedora systems. It updates
firmware (fwupd), Flatpak apps, and the rpm-ostree system in a single run, with
pre-flight checks, a rotating log, desktop notifications, and optional automation
via systemd timers.

### Features

- Updates firmware, Flatpak apps, and the rpm-ostree system in one run
- Pre-flight checks: network, disk space, battery level (laptops skip firmware below 30%),
  NTP clock sync, failed systemd units, journal errors, flatpak repo health
- Refuses to run if another instance is already running (`flock` guard)
- Rotating log at `~/.local/state/system-update.log` (1 MB, `.old` backup) with ANSI
  codes stripped for readability
- Desktop notifications on completion (including reboot-needed and failure states)
- Exit code is non-zero when any step failed — meaningful for automation
- `--check` mode reports pending updates without changing anything

### Requirements

- Fedora Atomic (Silverblue/Kinoite) or any rpm-ostree system
- `rpm-ostree`, `flatpak`, `fwupdmgr`, `mokutil`, `pkexec`

### Install

```sh
install -m 755 system-update ~/.local/bin/system-update
```

### Usage

```text
system-update [options]

  --auto     run unattended, no prompts (for timers/scripts)
  --check    only report available updates, change nothing
  --repair   additionally deep-verify Flatpak installs
  --help     show this help
```

The system update step uses `pkexec`, so it prompts for your password (or runs
unattended via polkit when invoked by a timer in an active graphical session).

### Automation

`extras/systemd/` contains user units for a weekly full update (Mon 03:00) and a
daily update check (09:00, `--auto --check`). Install and enable them with:

```sh
mkdir -p ~/.config/systemd/user
cp extras/systemd/* ~/.config/systemd/user/
systemctl --user daemon-reload
systemctl --user enable --now system-update.timer system-update-check.timer
```

`extras/system-update.desktop` adds a launcher to the app grid (uses `ptyxis`).
The shipped units and desktop file reference `/home/<user>/.local/bin/system-update` —
edit them if your user or install path differs.

### Logging

All output is appended to `~/.local/state/system-update.log` and rotated once it
exceeds 1 MB.

### Exit codes

- `0` — all steps succeeded (or nothing to do)
- `1` — a step failed, the lock is held, pre-flight abort, or an unknown option
