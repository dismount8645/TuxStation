# TuxStation

A cohesive personal Fedora Atomic / Silverblue desktop workstation ecosystem.

TuxStation unifies:
- **`image/`**: Bootc / Fedora Atomic base container image definitions, system configurations, and BlueBuild recipes.
- **`tools/tuxility`**: Post-install setup, maintenance assistant, and automated system update scripts.
- **`dotfiles/distroconfig`**: Personal dotfiles, GNOME desktop configurations, and flatpak application manifests.

## Quick Start
```bash
# Display workflows
just

# Build custom container image
just build-image

# Run system maintenance assistant
just maintain

# Apply dotfiles and GNOME desktop settings
just dotfiles
```
