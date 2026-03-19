# WezTerm

`dvt` provides first-class WezTerm support with a dedicated `dvt wezterm` subcommand. It generates `wezterm.lua` from declarative YAML definitions using built-in presets and automatic theme color integration.

---

## Overview

WezTerm uses a Lua configuration file (`wezterm.lua`). `dvt` generates this file from YAML-defined configs using Go templates, with optional theme color interpolation. The built-in preset library provides common starting points.

---

## Available Presets

| Name | Description |
|------|-------------|
| `default` | Standard WezTerm configuration with sensible defaults |
| `minimal` | Minimal configuration for low-overhead setups |
| `tmux-style` | WezTerm with tmux-style keybindings (leader key, pane splits) |

---

## Commands

### List presets

```bash
dvt wezterm list
```

### Show a preset

View the YAML definition for a preset:

```bash
dvt wezterm show minimal
dvt wezterm show tmux-style
```

### Generate wezterm.lua

Generate `wezterm.lua` from a preset:

```bash
dvt wezterm generate minimal
```

### Apply a preset

Apply a preset and write `wezterm.lua` to the default output path:

```bash
dvt wezterm apply minimal
```

Specify a custom output path:

```bash
dvt wezterm apply minimal -o ~/.config/wezterm/wezterm.lua
```

---

## Automatic Theme Integration

When the active DevOpsMaestro theme includes a WezTerm color scheme mapping, `dvt wezterm apply` automatically sets the `color_scheme` in the generated Lua to match the active theme.

---

## Generated Config Structure

A generated `wezterm.lua` follows this structure:

```lua
local wezterm = require("wezterm")
local config = wezterm.config_builder()

-- Font
config.font = wezterm.font("MesloLGS Nerd Font Mono")
config.font_size = 14

-- Window
config.window_background_opacity = 0.95

-- Color scheme
config.color_scheme = "Catppuccin Mocha"

-- Tab bar
config.enable_tab_bar = true
config.tab_bar_at_bottom = false

-- Leader key (tmux-style preset)
config.leader = { key = "a", mods = "CTRL", timeout_milliseconds = 1000 }

-- Keybindings
config.keys = {
  { key = "|", mods = "LEADER", action = wezterm.action.SplitHorizontal { domain = "CurrentPaneDomain" } },
  { key = "-", mods = "LEADER", action = wezterm.action.SplitVertical { domain = "CurrentPaneDomain" } },
}

return config
```

---

## Preset Details

### default

Standard WezTerm configuration:

- Font: MesloLGS Nerd Font Mono, size 14
- Window opacity: 1.0
- Tab bar: enabled, top position
- No leader key
- Default WezTerm keybindings

### minimal

Low-overhead configuration:

- Font: MesloLGS Nerd Font Mono, size 13
- Window opacity: 1.0
- Tab bar: disabled
- No leader key
- Minimal keybindings

### tmux-style

WezTerm configured to behave like tmux:

- Font: MesloLGS Nerd Font Mono, size 14
- Window opacity: 0.95
- Tab bar: enabled
- Leader key: `Ctrl+a` (1000ms timeout)
- Keybindings:
  - `LEADER + |` -- Horizontal split
  - `LEADER + -` -- Vertical split
  - `LEADER + h/j/k/l` -- Navigate panes
  - `LEADER + z` -- Zoom pane

---

## Theme Color Mapping

When using theme integration, the following theme variables map to WezTerm settings:

| Theme Variable | WezTerm Setting |
|----------------|-----------------|
| `${theme.bg}` | `background` in color overrides |
| `${theme.fg}` | `foreground` in color overrides |
| `${theme.primary}` | `ansi[4]` (blue) |
| `${theme.accent}` | `ansi[5]` (magenta) |
| `${theme.success}` | `ansi[2]` (green) |
| `${theme.error}` | `ansi[1]` (red) |
| `${theme.warning}` | `ansi[3]` (yellow) |

---

## Custom Configuration

To create a custom WezTerm config beyond the presets, apply a YAML definition:

```yaml
apiVersion: devopsmaestro.dev/v1alpha1
kind: WeztermConfig
metadata:
  name: my-wezterm
  description: My custom WezTerm configuration
  category: developer
spec:
  font:
    family: "JetBrains Mono Nerd Font"
    size: 13
  window:
    opacity: 0.92
  colors:
    scheme: "Tokyo Night"
  leader:
    key: "b"
    mods: "CTRL"
    timeout: 1000
  keybindings:
    - key: "|"
      mods: "LEADER"
      action: "wezterm.action.SplitHorizontal{domain='CurrentPaneDomain'}"
    - key: "-"
      mods: "LEADER"
      action: "wezterm.action.SplitVertical{domain='CurrentPaneDomain'}"
  tabBar:
    enabled: true
    position: bottom
```

Apply via the emulator command:

```bash
dvt emulator apply -f my-wezterm.yaml
dvt emulator install my-wezterm
```

!!! note "WezTerm YAML uses a different apiVersion"
    WezTerm configs use `apiVersion: devopsmaestro.dev/v1alpha1` with `kind: WeztermConfig`, distinct from other `devopsmaestro.io/v1` resources.

---

## Troubleshooting

### wezterm.lua not picked up

WezTerm looks for its config at `~/.config/wezterm/wezterm.lua` or `~/.wezterm.lua`. Verify the output path:

```bash
dvt wezterm apply minimal -o ~/.config/wezterm/wezterm.lua
```

### Font not found

If the specified font family is not installed, WezTerm falls back to its built-in font. Install a Nerd Font from [nerdfonts.com](https://www.nerdfonts.com/) for full symbol support.

### Color scheme not found

WezTerm's built-in color scheme names are case-sensitive. Verify the scheme name matches exactly (e.g., `"Catppuccin Mocha"` not `"catppuccin-mocha"`).

---

## Related

- [Terminal Emulators](emulators.md) - General emulator management
- [Terminal Packages](packages.md) - Bundle WezTerm config with prompts and plugins
- [TerminalPackage YAML Reference](../reference/terminal-package.md)
