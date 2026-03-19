# Shell Plugins

`dvt` manages zsh shell plugins as resources in the shared database. Plugins are defined as `TerminalPlugin` resources and can be generated into install scripts for any supported plugin manager.

---

## What are Shell Plugins?

Shell plugins extend zsh with additional functionality: autosuggestions, syntax highlighting, fuzzy finding, completions, and more. `dvt` tracks which plugins you use and generates the appropriate plugin manager configuration.

---

## Commands

### List plugins

```bash
dvt plugin list
```

Output as YAML or JSON:

```bash
dvt plugin list -o yaml
dvt plugin list -o json
```

### Get a specific plugin

```bash
dvt plugin get zsh-autosuggestions
dvt plugin get zsh-autosuggestions -o yaml
```

### Generate plugin manager configuration

Generate install scripts for your plugin manager:

```bash
dvt plugin generate
```

Specify the plugin manager with `--manager`:

```bash
dvt plugin generate --manager zinit
dvt plugin generate --manager oh-my-zsh
dvt plugin generate --manager antigen
dvt plugin generate --manager sheldon
dvt plugin generate --manager manual
```

Generate configuration for specific plugins only:

```bash
dvt plugin generate zsh-autosuggestions zsh-syntax-highlighting --manager zinit
```

---

## Supported Plugin Managers

| Manager | Flag value | Description |
|---------|------------|-------------|
| [zinit](https://github.com/zdharma-continuum/zinit) | `zinit` | Fast async zsh plugin manager |
| [Oh-My-Zsh](https://ohmyz.sh/) | `oh-my-zsh` | Popular zsh framework |
| [Antigen](https://antigen.sharats.me/) | `antigen` | Zsh plugin manager inspired by Vundle |
| [Sheldon](https://sheldon.cli.rs/) | `sheldon` | Fast, configurable shell plugin manager |
| Manual | `manual` | Raw `source` statements for manual management |

---

## Plugin Library

`dvt` includes a built-in library of curated zsh plugins.

### Browse the library

```bash
dvt plugin library list
```

Filter by category:

```bash
dvt plugin library list -c productivity
dvt plugin library list -c completion
dvt plugin library list -c appearance
```

List available categories:

```bash
dvt plugin library categories
```

### View a library plugin

```bash
dvt plugin library show zsh-autosuggestions
dvt plugin library show zsh-autosuggestions -o yaml
```

### Install from the library

Install one or more plugins into your local database:

```bash
dvt plugin library install zsh-autosuggestions
dvt plugin library install zsh-autosuggestions zsh-syntax-highlighting fzf
```

Install all library plugins:

```bash
dvt plugin library install --all
```

---

## Related

- [Shell Configuration](configuration.md) - Manage aliases, env vars, and shell settings
- [Terminal Profiles](../terminal/profiles.md) - Bundle plugins with prompts and shell config
