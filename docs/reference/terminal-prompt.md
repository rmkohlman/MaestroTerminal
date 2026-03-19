# TerminalPrompt YAML Reference

`TerminalPrompt` is the core resource type for managing shell prompt configurations in `dvt`. It supports Starship, Powerlevel10k, and Oh-My-Posh prompt engines.

---

## Full Annotated Example

```yaml
apiVersion: devopsmaestro.io/v1
kind: TerminalPrompt
metadata:
  name: my-prompt                          # Unique name (required)
  description: My custom Starship prompt   # Human-readable description
  category: developer                      # Category for filtering
  labels:                                  # Optional key-value labels
    team: backend
    env: dev
spec:
  type: starship                           # Prompt engine (required)
  addNewline: true                         # Add blank line before prompt
  palette: theme                           # Palette mode: "theme" or "custom"
  paletteRef: catppuccin-mocha             # Optional: pin to a specific palette
  format: "$directory$git_branch$character" # Starship format string
  modules:                                 # Per-module configuration
    directory:
      format: "[$path]($style) "
      style: "${theme.primary}"
    git_branch:
      symbol: " "
      format: "[$symbol$branch]($style) "
      style: "${theme.accent}"
    nodejs:
      disabled: false
      format: "[$symbol($version )]($style)"
      style: "${theme.success}"
      symbol: " "
    character:
      successSymbol: "[>](bold ${theme.success})"
      errorSymbol: "[>](bold ${theme.error})"
  character:                               # Top-level character config
    successSymbol: "[>](bold green)"
    errorSymbol: "[>](bold red)"
    viCmdSymbol: "[N](bold blue)"
    viInsSymbol: "[I](bold cyan)"
  colors:                                  # Direct color overrides (hex values)
    primary: "#89b4fa"
    accent: "#cba6f7"
  rawConfig: |                             # Raw TOML appended verbatim
    [custom.git_email]
    command = "git config user.email"
    format = "[$output]($style) "
    style = "bold yellow"
    when = "git rev-parse --is-inside-work-tree 2>/dev/null"
```

---

## Field Reference

### metadata fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Unique identifier for this prompt |
| `description` | string | No | Human-readable description |
| `category` | string | No | Category for library filtering |
| `labels` | map | No | Arbitrary key-value labels |

### spec fields

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `type` | string | Yes | | Prompt engine: `starship`, `powerlevel10k`, `oh-my-posh` |
| `addNewline` | bool | No | `true` | Add a blank line before the prompt |
| `palette` | string | No | `theme` | Palette mode: `theme` uses the active palette |
| `paletteRef` | string | No | | Pin to a specific named palette resource |
| `format` | string | No | | Starship format string |
| `modules` | map | No | | Per-module configuration (see below) |
| `character` | object | No | | Top-level character configuration (see below) |
| `colors` | map | No | | Direct color overrides as hex values |
| `rawConfig` | string | No | | Raw TOML/config content appended verbatim |

---

## Module Configuration Fields

Each entry in the `modules` map configures a single prompt module:

| Field | Type | Description |
|-------|------|-------------|
| `disabled` | bool | Disable this module entirely |
| `format` | string | Module format string |
| `style` | string | Module style (supports `${theme.X}` variables) |
| `symbol` | string | Override the module's symbol |
| `options` | map | Additional module-specific key-value options |

**Example:**

```yaml
modules:
  git_status:
    format: '([\[$all_status$ahead_behind\]]($style) )'
    style: "${theme.warning}"
    options:
      conflicted: "="
      ahead: "⇡${count}"
      behind: "⇣${count}"
      diverged: "⇕⇡${ahead_count}⇣${behind_count}"
      untracked: "?"
      modified: "!"
      staged: "+"
```

---

## Character Configuration Fields

The `character` field configures the final prompt symbol:

| Field | Type | Description |
|-------|------|-------------|
| `successSymbol` | string | Symbol after a successful command |
| `errorSymbol` | string | Symbol after a failed command |
| `viCmdSymbol` | string | Symbol in vi command mode |
| `viInsSymbol` | string | Symbol in vi insert mode |

---

## Theme Variables

Use `${theme.variable}` in any `style` or color field:

| Variable | Description |
|----------|-------------|
| `${theme.primary}` | Primary brand color |
| `${theme.secondary}` | Secondary brand color |
| `${theme.accent}` | Accent/highlight color |
| `${theme.bg}` | Background color |
| `${theme.fg}` | Foreground color |
| `${theme.success}` | Success state (green-family) |
| `${theme.error}` | Error state (red-family) |
| `${theme.warning}` | Warning state (yellow-family) |
| `${theme.info}` | Info state (blue-family) |
| `${theme.red}` through `${theme.white}` | Named ANSI colors |
| `${theme.bright_red}` through `${theme.bright_white}` | Bright ANSI variants |
| `${theme.crust}`, `${theme.mantle}`, `${theme.base}` | Catppuccin surface colors |
| `${theme.surface0}` through `${theme.surface2}` | Surface layers |
| `${theme.overlay0}` through `${theme.overlay2}` | Overlay layers |
| `${theme.text}`, `${theme.subtext0}`, `${theme.subtext1}` | Text colors |
| `${theme.lavender}`, `${theme.sky}`, `${theme.sapphire}`, `${theme.teal}` | Accent colors |
| `${theme.peach}`, `${theme.maroon}`, `${theme.pink}`, `${theme.mauve}` | Warm accents |
| `${theme.flamingo}`, `${theme.rosewater}` | Soft accents |

---

## Examples

### Minimal single-line prompt

```yaml
apiVersion: devopsmaestro.io/v1
kind: TerminalPrompt
metadata:
  name: minimal
  description: Clean minimal prompt
  category: minimal
spec:
  type: starship
  addNewline: false
  palette: theme
  format: "$directory$git_branch$character"
  modules:
    directory:
      format: "[$path]($style) "
      style: "${theme.primary}"
    git_branch:
      format: "[$branch]($style) "
      style: "${theme.accent}"
    character:
      successSymbol: "[>](bold ${theme.success})"
      errorSymbol: "[>](bold ${theme.error})"
```

### Multi-line prompt with status line

```yaml
apiVersion: devopsmaestro.io/v1
kind: TerminalPrompt
metadata:
  name: multi-line
  description: Two-line prompt with full status
spec:
  type: starship
  addNewline: true
  palette: theme
  format: |
    $directory$git_branch$git_status$nodejs$python$rust
    $character
  modules:
    directory:
      format: "[$path]($style) "
      style: "bold ${theme.primary}"
    git_branch:
      symbol: " "
      format: "[$symbol$branch]($style) "
      style: "${theme.accent}"
    git_status:
      format: '([\[$all_status$ahead_behind\]]($style) )'
      style: "${theme.warning}"
    nodejs:
      format: "[$symbol($version )]($style)"
      style: "${theme.success}"
```

### Workspace context prompt

```yaml
apiVersion: devopsmaestro.io/v1
kind: TerminalPrompt
metadata:
  name: workspace-context
  description: Prompt with dvm workspace context
  category: developer
spec:
  type: starship
  palette: theme
  format: "$custom$directory$git_branch$character"
  modules:
    directory:
      style: "${theme.primary}"
    git_branch:
      style: "${theme.accent}"
  rawConfig: |
    [custom.workspace]
    command = "dvm get workspace --short 2>/dev/null"
    format = "[ws:$output]($style) "
    style = "bold ${theme.info}"
    when = "dvm get workspace --short 2>/dev/null"
```

### Raw TOML passthrough

```yaml
apiVersion: devopsmaestro.io/v1
kind: TerminalPrompt
metadata:
  name: with-raw-config
spec:
  type: starship
  palette: theme
  format: "$directory$git_branch$character"
  rawConfig: |
    [aws]
    format = '[$symbol($profile )(\($region\) )(\[$duration\] )]($style)'
    style = "bold yellow"
    symbol = "☁️  "
    
    [gcloud]
    format = '[$symbol$account(@$domain)(\($region\))]($style) '
    style = "${theme.info}"
```

---

## Validation Rules

- `metadata.name` is required and must be unique
- `spec.type` is required and must be one of: `starship`, `powerlevel10k`, `oh-my-posh`
- `spec.palette` defaults to `theme` if not specified
- `spec.paletteRef` is only relevant when `palette: theme`; it pins the palette by name
- `spec.rawConfig` is appended verbatim to the generated config file -- invalid TOML will cause generation errors

---

## Related

- [Prompts Overview](../prompts/overview.md) - Command reference
- [Theme Integration](../prompts/theme-integration.md) - Full theme variable guide
- [Terminal Profiles](../terminal/profiles.md) - Using prompts in profiles
