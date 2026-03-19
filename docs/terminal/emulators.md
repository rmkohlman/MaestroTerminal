# Terminal Emulators

`dvt` manages terminal emulator configurations as `TerminalEmulator` resources. You can apply, enable, disable, and install emulator configurations, and browse the built-in library of curated emulator configs.

---

## Supported Emulators

| Emulator | Type value | Platform |
|----------|------------|---------|
| [WezTerm](https://wezfurlong.org/wezterm/) | `wezterm` | macOS, Linux, Windows |
| [Alacritty](https://alacritty.org/) | `alacritty` | macOS, Linux, Windows |
| [Kitty](https://sw.kovidgoyal.net/kitty/) | `kitty` | macOS, Linux |
| [iTerm2](https://iterm2.com/) | `iterm2` | macOS only |

!!! note "WezTerm-specific commands"
    WezTerm has its own dedicated subcommand (`dvt wezterm`) with preset-based Lua generation. See [WezTerm](wezterm.md) for details.

---

## Commands

### List emulator configurations

```bash
dvt emulator list
```

Filter by emulator type:

```bash
dvt emulator list --type wezterm
dvt emulator list --type alacritty
dvt emulator list --type kitty
dvt emulator list --type iterm2
```

Filter by category:

```bash
dvt emulator list --category minimal
```

Output as YAML or JSON:

```bash
dvt emulator list -o yaml
dvt emulator list -o json
```

### Get a specific emulator configuration

```bash
dvt emulator get my-wezterm
dvt emulator get my-wezterm -o yaml
```

### Enable an emulator configuration

Mark an emulator configuration as enabled:

```bash
dvt emulator enable my-wezterm
```

### Disable an emulator configuration

```bash
dvt emulator disable my-wezterm
```

### Install an emulator configuration

Install a named emulator configuration (writes the config file to the appropriate location):

```bash
dvt emulator install my-wezterm
```

Force overwrite an existing config:

```bash
dvt emulator install my-wezterm --force
```

Preview what would be installed without writing:

```bash
dvt emulator install my-wezterm --dry-run
```

### Apply an emulator config from YAML

```bash
dvt emulator apply -f emulator.yaml
```

Preview without writing to the database:

```bash
dvt emulator apply -f emulator.yaml --dry-run
```

---

## Emulator Library

The built-in library contains curated emulator configurations.

### Browse the library

```bash
dvt emulator library list
```

Filter by type:

```bash
dvt emulator library list --type wezterm
dvt emulator library list --type alacritty
```

### View a library entry

```bash
dvt emulator library show wezterm-minimal
dvt emulator library show wezterm-minimal -o yaml
```

---

## YAML Format

```yaml
apiVersion: devopsmaestro.io/v1
kind: TerminalEmulator
metadata:
  name: my-alacritty
  description: Alacritty configuration
  category: minimal
spec:
  type: alacritty
  font:
    family: "MesloLGS Nerd Font Mono"
    size: 14
  window:
    opacity: 0.95
    padding:
      x: 8
      y: 8
  colors:
    scheme: "Catppuccin Mocha"
```

---

## Related

- [WezTerm](wezterm.md) - WezTerm-specific Lua generation with presets
- [Terminal Packages](packages.md) - Bundle emulator config with prompts and plugins
