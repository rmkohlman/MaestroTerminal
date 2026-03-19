# Prompts Overview

`dvt` manages terminal prompts as `TerminalPrompt` YAML resources. A `TerminalPrompt` defines the full configuration for a shell prompt engine -- its format, modules, colors, and behavior.

## Supported Prompt Engines

| Engine | Type value | Description |
|--------|------------|-------------|
| [Starship](https://starship.rs/) | `starship` | Fast, cross-shell prompt written in Rust |
| [Powerlevel10k](https://github.com/romkatv/powerlevel10k) | `powerlevel10k` | Zsh-only, highly customizable powerline prompt |
| [Oh-My-Posh](https://ohmyposh.dev/) | `oh-my-posh` | Cross-shell prompt engine with JSON/YAML themes |

---

## TerminalPrompt YAML Format

```yaml
apiVersion: devopsmaestro.io/v1
kind: TerminalPrompt
metadata:
  name: my-prompt
  description: My custom Starship prompt
  category: developer
spec:
  type: starship
  addNewline: true
  palette: theme
  format: "$directory$git_branch$character"
  modules:
    directory:
      format: "[$path]($style) "
      style: "${theme.primary}"
    git_branch:
      symbol: " "
      format: "[$symbol$branch]($style) "
      style: "${theme.accent}"
    character:
      successSymbol: "[>](bold ${theme.success})"
      errorSymbol: "[>](bold ${theme.error})"
```

See the full field reference at [YAML Reference: TerminalPrompt](../reference/terminal-prompt.md).

---

## Commands

### Apply a prompt from YAML

```bash
dvt prompt apply -f prompt.yaml
```

Apply multiple files or a directory:

```bash
dvt prompt apply -f ./prompts/
```

### List prompts

```bash
dvt prompt list
```

Output as YAML or JSON:

```bash
dvt prompt list -o yaml
dvt prompt list -o json
```

Filter by type:

```bash
dvt get prompts --type starship
```

### Get a specific prompt

```bash
dvt prompt get my-prompt
dvt prompt get my-prompt -o yaml
```

### Generate a config file

Generate `starship.toml` (or equivalent) from a saved prompt:

```bash
dvt prompt generate my-prompt
```

The generated file is written to the appropriate config location for the prompt engine.

### Set the active prompt

Mark a prompt as the active prompt:

```bash
dvt prompt set my-prompt
```

Force replacement if another prompt is already active:

```bash
dvt prompt set my-prompt --force
```

### Delete a prompt

```bash
dvt prompt delete my-prompt
```

Force deletion without confirmation:

```bash
dvt prompt delete my-prompt --force
```

---

## Theme Variable Interpolation

Prompt styles support `${theme.color}` variables. These are resolved against the active palette at generation time, making prompts portable across color schemes.

```yaml
modules:
  directory:
    style: "${theme.primary}"
  git_branch:
    style: "${theme.accent}"
  character:
    successSymbol: "[>](bold ${theme.success})"
    errorSymbol: "[>](bold ${theme.error})"
```

See [Theme Integration](theme-integration.md) for the full variable reference.

---

## Module Configuration

The `modules` field is a map of module names to their configuration. Each module supports:

| Field | Type | Description |
|-------|------|-------------|
| `disabled` | bool | Disable this module entirely |
| `format` | string | Module format string |
| `style` | string | Module style (supports `${theme.X}` variables) |
| `symbol` | string | Symbol override |
| `options` | map | Additional module-specific options |

Example with options:

```yaml
modules:
  nodejs:
    format: "[$symbol($version )]($style)"
    style: "${theme.success}"
    symbol: " "
    options:
      detect_files: ["package.json", ".nvmrc"]
  python:
    format: "[$symbol$version]($style) "
    style: "${theme.accent}"
    disabled: false
```

---

## Character Configuration

The `character` field configures the prompt character (the final symbol before your cursor):

```yaml
spec:
  character:
    successSymbol: "[>](bold ${theme.success})"
    errorSymbol: "[>](bold ${theme.error})"
    viCmdSymbol: "[N](bold ${theme.accent})"
    viInsSymbol: "[I](bold ${theme.primary})"
```

| Field | Description |
|-------|-------------|
| `successSymbol` | Symbol shown after a successful command |
| `errorSymbol` | Symbol shown after a failed command |
| `viCmdSymbol` | Symbol in vi command mode |
| `viInsSymbol` | Symbol in vi insert mode |

---

## Next Steps

- [Prompt Library](library.md) - Browse and install built-in prompts
- [Theme Integration](theme-integration.md) - Use theme color variables
- [Terminal Profiles](../terminal/profiles.md) - Bundle prompts with plugins and shell config
