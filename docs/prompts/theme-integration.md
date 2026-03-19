# Theme Integration

`dvt` prompts support `${theme.color}` variable interpolation, making prompt configurations portable across color schemes. When you generate a prompt, the variables are resolved against the active palette.

---

## How Theme Variables Work

In your `TerminalPrompt` YAML, use `${theme.color}` syntax anywhere a color value is expected:

```yaml
spec:
  modules:
    directory:
      style: "${theme.primary}"
    git_branch:
      style: "${theme.accent}"
    character:
      successSymbol: "[>](bold ${theme.success})"
      errorSymbol: "[>](bold ${theme.error})"
```

When `dvt prompt generate <name>` runs, the variables are resolved against the active palette and written into the generated config file (`starship.toml`, etc.).

---

## Available Theme Variables

| Variable | Description |
|----------|-------------|
| `${theme.primary}` | Primary brand color |
| `${theme.secondary}` | Secondary brand color |
| `${theme.accent}` | Accent/highlight color |
| `${theme.bg}` | Background color |
| `${theme.fg}` | Foreground/text color |
| `${theme.success}` | Success state color (green-family) |
| `${theme.error}` | Error state color (red-family) |
| `${theme.warning}` | Warning state color (yellow-family) |
| `${theme.info}` | Info state color (blue-family) |
| `${theme.red}` | Named red |
| `${theme.green}` | Named green |
| `${theme.blue}` | Named blue |
| `${theme.yellow}` | Named yellow |
| `${theme.orange}` | Named orange |
| `${theme.purple}` | Named purple |
| `${theme.cyan}` | Named cyan |
| `${theme.white}` | Named white |
| `${theme.black}` | Named black |
| `${theme.bright_red}` | Bright red variant |
| `${theme.bright_green}` | Bright green variant |
| `${theme.bright_blue}` | Bright blue variant |
| `${theme.bright_yellow}` | Bright yellow variant |
| `${theme.bright_cyan}` | Bright cyan variant |
| `${theme.bright_white}` | Bright white variant |
| `${theme.crust}` | Catppuccin-style darkest surface |
| `${theme.mantle}` | Catppuccin-style dark surface |
| `${theme.base}` | Catppuccin-style base background |
| `${theme.surface0}` | Surface layer 0 |
| `${theme.surface1}` | Surface layer 1 |
| `${theme.surface2}` | Surface layer 2 |
| `${theme.overlay0}` | Overlay layer 0 |
| `${theme.overlay1}` | Overlay layer 1 |
| `${theme.overlay2}` | Overlay layer 2 |
| `${theme.text}` | Primary text color |
| `${theme.subtext0}` | Subtext color 0 |
| `${theme.subtext1}` | Subtext color 1 |
| `${theme.lavender}` | Lavender accent |
| `${theme.sky}` | Sky blue accent |
| `${theme.sapphire}` | Sapphire accent |
| `${theme.teal}` | Teal accent |
| `${theme.peach}` | Peach accent |
| `${theme.maroon}` | Maroon accent |
| `${theme.pink}` | Pink accent |
| `${theme.mauve}` | Mauve accent |
| `${theme.flamingo}` | Flamingo soft accent |
| `${theme.rosewater}` | Rosewater soft accent |

---

## Setting the Palette

### Use the active theme palette

Set `palette: theme` to resolve variables against the currently active theme:

```yaml
spec:
  palette: theme
```

This is the default when using `palette: theme`. The active theme is determined by the DevOpsMaestro theme hierarchy (workspace > app > domain > ecosystem > global).

### Reference a specific palette

Use `paletteRef` to pin a prompt to a specific named palette regardless of the active theme:

```yaml
spec:
  palette: theme
  paletteRef: catppuccin-mocha
```

---

## Custom Color Overrides

Use the `colors` field to override specific theme variables with fixed hex values. This is useful for prompt elements that should always use a specific color regardless of theme:

```yaml
spec:
  palette: theme
  colors:
    primary: "#89b4fa"
    accent: "#cba6f7"
    success: "#a6e3a1"
    error: "#f38ba8"
```

Variables not listed in `colors` continue to be resolved from the active palette.

---

## Regenerating After a Theme Change

When the active theme changes, regenerate the prompt config to pick up the new colors:

```bash
dvt prompt generate my-prompt
```

---

## Examples

### Starship with theme colors

```yaml
apiVersion: devopsmaestro.io/v1
kind: TerminalPrompt
metadata:
  name: dev-prompt
spec:
  type: starship
  addNewline: true
  palette: theme
  format: "$directory$git_branch$nodejs$python$character"
  modules:
    directory:
      format: "[$path]($style) "
      style: "bold ${theme.primary}"
    git_branch:
      symbol: " "
      format: "[$symbol$branch]($style) "
      style: "${theme.accent}"
    nodejs:
      format: "[$symbol($version )]($style)"
      style: "${theme.success}"
    python:
      format: "[$symbol$version]($style) "
      style: "${theme.teal}"
    character:
      successSymbol: "[>](bold ${theme.success})"
      errorSymbol: "[>](bold ${theme.error})"
```

### Powerlevel10k with theme colors

```yaml
apiVersion: devopsmaestro.io/v1
kind: TerminalPrompt
metadata:
  name: p10k-themed
spec:
  type: powerlevel10k
  palette: theme
  modules:
    dir:
      style: "${theme.primary}"
    vcs:
      style: "${theme.accent}"
```

### Oh-My-Posh with theme colors

```yaml
apiVersion: devopsmaestro.io/v1
kind: TerminalPrompt
metadata:
  name: omp-themed
spec:
  type: oh-my-posh
  palette: theme
  modules:
    path:
      foreground: "${theme.primary}"
      background: "${theme.surface0}"
    git:
      foreground: "${theme.accent}"
      background: "${theme.surface1}"
```

### Pinned to a specific palette

```yaml
apiVersion: devopsmaestro.io/v1
kind: TerminalPrompt
metadata:
  name: mocha-prompt
spec:
  type: starship
  palette: theme
  paletteRef: catppuccin-mocha
  modules:
    directory:
      style: "${theme.lavender}"
    git_branch:
      style: "${theme.mauve}"
```

---

## Related

- [Prompts Overview](overview.md) - Full prompt command reference
- [TerminalPrompt YAML Reference](../reference/terminal-prompt.md) - Complete field reference
