# Tuxility

A post-install setup assistant for Fedora Atomic desktops (Silverblue, Kinoite,
Bazzite, etc.). It presents a click-to-run checklist of common first tasks —
enabling Flathub, installing apps, layering packages with `rpm-ostree`, and
applying desktop settings — so you don't have to remember a wall of commands
after a fresh install.

## Features

- Task catalog in a single editable `tasks.toml` — no code needed to add tasks
- Click-to-run: tick the tasks you want, hit **Run selected**, watch live output
- Each task can layer packages (`pkexec rpm-ostree`), install Flatpak apps,
  apply `gsettings`, or run any shell command
- Per-task `check` commands mark already-installed/configured items as done
- Done state persisted at `~/.local/state/tuxility-setup.json`
- Confirmation prompts for risky tasks, reboot prompt when layering requires it
- Runs entirely from the immutable base image — no system packages to layer

## Requirements

- Fedora Atomic (Silverblue/Kinoite) or any rpm-ostree system
- `python3-gobject`, `gtk4`, `libadwaita` (present in the base image), `pkexec`
- `flatpak` and `rpm-ostree` for the default catalog

## Install

```sh
install -m 755 tuxility-setup ~/.local/bin/tuxility-setup
cp tasks.toml ~/.config/tuxility/tasks.toml
cp extras/tuxility-setup.desktop ~/.local/share/applications/
```

Or just run `./tuxility-setup` from the repo. The script looks for tasks in
`~/.config/tuxility/tasks.toml` first, then next to the script.

## Usage

- **Check** — re-evaluate every task's `check` command and refresh the done marks
- **Run selected** — execute the ticked tasks in order, streaming output to the log
- Menu (⋮): **Select all**, **Clear selection**, **Reset done state**

`tuxility-setup --list` prints the catalog for quick review.

## Writing tasks

`tasks.toml` groups tasks under `[[group]]`; each task is a `[[group.item]]`:

```toml
[[group.item]]
id = "my-app"                    # unique id, used for the done state
name = "My app"                  # row title
detail = "One-line description"  # row subtitle
command = "flatpak install --user -y flathub org.example.App"   # what to run
sudo = false                     # run via pkexec (e.g. rpm-ostree layer)
check = "flatpak info --user org.example.App >/dev/null 2>&1"   # exit 0 = done
reboot = false                   # warn + offer reboot after the run
confirm = "Explain what this does"   # optional pre-run confirmation text
```

`command` and `check` are shell strings (run with `bash -lc`). Tasks with
`sudo = true` are wrapped in `pkexec`, which pops the polkit password dialog.
