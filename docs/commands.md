# Commands Reference

Complete reference for all `dvt` commands, flags, and examples.

---

## Global Flags

These flags are available on every `dvt` command:

| Flag | Short | Description |
|------|-------|-------------|
| `--config` | | Config directory path (default: `~/.dvt/`) |
| `--verbose` | `-v` | Enable verbose output |
| `--log-file` | | Write logs to a file |

---

## dvt version

Print version information.

```bash
dvt version
dvt version --short
```

| Flag | Description |
|------|-------------|
| `--short` | Print version number only |

---

## dvt init

Initialize the `dvt` config directory at `~/.dvt/` (or the path specified by `--config`).

```bash
dvt init
dvt init --config /custom/path
```

---

## dvt completion

Generate shell completion scripts.

```bash
dvt completion [bash|zsh|fish|powershell]
```

**Examples:**

```bash
# Zsh
eval "$(dvt completion zsh)"

# Bash
eval "$(dvt completion bash)"

# Fish
dvt completion fish | source
```

---

## dvt get prompts

List terminal prompts from the database. This is an alternative to `dvt prompt list`.

```bash
dvt get prompts
dvt get prompts -o yaml
dvt get prompts --type starship
dvt get prompts --type powerlevel10k
```

| Flag | Short | Description |
|------|-------|-------------|
| `--output` | `-o` | Output format: `table`, `yaml`, `json` (default: `table`) |
| `--type` | | Filter by prompt type: `starship`, `powerlevel10k` |

---

## dvt prompt

Manage terminal prompt resources.

### dvt prompt list

List all saved prompts.

```bash
dvt prompt list
dvt prompt list -o yaml
dvt prompt list -o json
```

| Flag | Short | Description |
|------|-------|-------------|
| `--output` | `-o` | Output format: `table`, `yaml`, `json` |

### dvt prompt get

Get a specific prompt by name.

```bash
dvt prompt get <name>
dvt prompt get my-prompt -o yaml
dvt prompt get my-prompt -o json
```

| Flag | Short | Description |
|------|-------|-------------|
| `--output` | `-o` | Output format: `yaml`, `json`, `table` |

### dvt prompt apply

Apply a prompt from a YAML file.

```bash
dvt prompt apply -f prompt.yaml
dvt prompt apply -f ./prompts/
```

| Flag | Short | Description |
|------|-------|-------------|
| `--filename` | `-f` | Path to YAML file or directory (required) |

### dvt prompt delete

Delete a prompt by name.

```bash
dvt prompt delete my-prompt
dvt prompt delete my-prompt --force
```

| Flag | Short | Description |
|------|-------|-------------|
| `--force` | `-f` | Skip confirmation prompt |

### dvt prompt generate

Generate the config file (e.g., `starship.toml`) for a saved prompt.

```bash
dvt prompt generate my-prompt
```

### dvt prompt set

Set the active prompt.

```bash
dvt prompt set my-prompt
dvt prompt set my-prompt --force
```

| Flag | Short | Description |
|------|-------|-------------|
| `--force` | `-f` | Replace the currently active prompt without confirmation |

### dvt prompt library list

List prompts in the built-in library.

```bash
dvt prompt library list
dvt prompt library list -c minimal
dvt prompt library list -o yaml
```

| Flag | Short | Description |
|------|-------|-------------|
| `--output` | `-o` | Output format: `table`, `yaml`, `json` |
| `--category` | `-c` | Filter by category |

### dvt prompt library show

Show a library prompt's full definition.

```bash
dvt prompt library show starship-minimal
dvt prompt library show starship-minimal -o yaml
```

| Flag | Short | Description |
|------|-------|-------------|
| `--output` | `-o` | Output format: `yaml`, `json` |

### dvt prompt library install

Install one or more prompts from the library into the local database.

```bash
dvt prompt library install starship-minimal
dvt prompt library install starship-minimal starship-powerline
dvt prompt library install --all
```

| Flag | Description |
|------|-------------|
| `--all` | Install all library prompts |

### dvt prompt library categories

List available prompt library categories.

```bash
dvt prompt library categories
```

---

## dvt plugin

Manage shell plugins (zsh).

### dvt plugin list

```bash
dvt plugin list
dvt plugin list -o yaml
```

| Flag | Short | Description |
|------|-------|-------------|
| `--output` | `-o` | Output format: `table`, `yaml`, `json` |

### dvt plugin get

```bash
dvt plugin get zsh-autosuggestions
dvt plugin get zsh-autosuggestions -o yaml
```

| Flag | Short | Description |
|------|-------|-------------|
| `--output` | `-o` | Output format: `yaml`, `json` |

### dvt plugin generate

Generate plugin manager configuration.

```bash
dvt plugin generate
dvt plugin generate --manager zinit
dvt plugin generate zsh-autosuggestions fzf --manager zinit
```

| Flag | Short | Description |
|------|-------|-------------|
| `--manager` | `-m` | Plugin manager: `zinit`, `oh-my-zsh`, `antigen`, `sheldon`, `manual` |

### dvt plugin library list

```bash
dvt plugin library list
dvt plugin library list -c productivity
dvt plugin library list -o yaml
```

| Flag | Short | Description |
|------|-------|-------------|
| `--output` | `-o` | Output format: `table`, `yaml`, `json` |
| `--category` | `-c` | Filter by category |

### dvt plugin library show

```bash
dvt plugin library show zsh-autosuggestions
dvt plugin library show zsh-autosuggestions -o yaml
```

| Flag | Short | Description |
|------|-------|-------------|
| `--output` | `-o` | Output format: `yaml`, `json` |

### dvt plugin library install

```bash
dvt plugin library install zsh-autosuggestions
dvt plugin library install zsh-autosuggestions fzf zsh-syntax-highlighting
dvt plugin library install --all
```

| Flag | Description |
|------|-------------|
| `--all` | Install all library plugins |

### dvt plugin library categories

```bash
dvt plugin library categories
```

---

## dvt shell

Manage shell configurations.

### dvt shell apply

```bash
dvt shell apply -f shell.yaml
```

| Flag | Short | Description |
|------|-------|-------------|
| `--filename` | `-f` | Path to YAML file (required) |

### dvt shell list

```bash
dvt shell list
dvt shell list -o yaml
```

| Flag | Short | Description |
|------|-------|-------------|
| `--output` | `-o` | Output format: `table`, `yaml`, `json` |

### dvt shell get

```bash
dvt shell get my-shell
dvt shell get my-shell -o yaml
```

| Flag | Short | Description |
|------|-------|-------------|
| `--output` | `-o` | Output format: `yaml`, `json` |

### dvt shell generate

Generate shell script output for a saved configuration.

```bash
dvt shell generate my-shell
```

---

## dvt profile

Manage terminal profiles.

### dvt profile apply

```bash
dvt profile apply -f profile.yaml
```

| Flag | Short | Description |
|------|-------|-------------|
| `--filename` | `-f` | Path to YAML file (required) |

### dvt profile list

```bash
dvt profile list
dvt profile list -o yaml
```

| Flag | Short | Description |
|------|-------|-------------|
| `--output` | `-o` | Output format: `table`, `yaml`, `json` |

### dvt profile get

```bash
dvt profile get my-profile
dvt profile get my-profile -o yaml
```

| Flag | Short | Description |
|------|-------|-------------|
| `--output` | `-o` | Output format: `yaml`, `json` |

### dvt profile generate

Generate all config files for a profile.

```bash
dvt profile generate my-profile
dvt profile generate my-profile -o ~/.config/terminal
dvt profile generate my-profile --dry-run
```

| Flag | Short | Description |
|------|-------|-------------|
| `--output` | `-o` | Output directory path |
| `--dry-run` | | Print what would be generated without writing |

### dvt profile use

Set the active profile.

```bash
dvt profile use my-profile
```

### dvt profile preset list

```bash
dvt profile preset list
```

### dvt profile preset install

```bash
dvt profile preset install default
dvt profile preset install minimal
```

---

## dvt package

Manage terminal packages. The alias `dvt pkg` works for all subcommands.

### dvt package list

```bash
dvt package list
dvt package list -c developer
dvt package list -w
dvt package list --library
dvt package list --user
dvt package list -o yaml
```

| Flag | Short | Description |
|------|-------|-------------|
| `--output` | `-o` | Output format: `table`, `yaml`, `json` |
| `--category` | `-c` | Filter by category |
| `--wide` | `-w` | Show extended columns |
| `--library` | | Show only library packages |
| `--user` | | Show only user-installed packages |

### dvt package get

```bash
dvt package get developer
dvt package get developer -o yaml
```

| Flag | Short | Description |
|------|-------|-------------|
| `--output` | `-o` | Output format: `yaml`, `json` |

### dvt package install

```bash
dvt package install developer
dvt package install developer --dry-run
```

| Flag | Description |
|------|-------------|
| `--dry-run` | Preview what would be installed without making changes |

---

## dvt emulator

Manage terminal emulator configurations. The alias `dvt emu` works for all subcommands.

### dvt emulator list

```bash
dvt emulator list
dvt emulator list --type wezterm
dvt emulator list --category minimal
dvt emulator list -o yaml
```

| Flag | Short | Description |
|------|-------|-------------|
| `--output` | `-o` | Output format: `table`, `yaml`, `json` |
| `--type` | | Filter by type: `wezterm`, `alacritty`, `kitty`, `iterm2` |
| `--category` | | Filter by category |

### dvt emulator get

```bash
dvt emulator get my-wezterm
dvt emulator get my-wezterm -o yaml
```

| Flag | Short | Description |
|------|-------|-------------|
| `--output` | `-o` | Output format: `yaml`, `json` |

### dvt emulator enable

```bash
dvt emulator enable my-wezterm
```

### dvt emulator disable

```bash
dvt emulator disable my-wezterm
```

### dvt emulator install

```bash
dvt emulator install my-wezterm
dvt emulator install my-wezterm --force
dvt emulator install my-wezterm --dry-run
```

| Flag | Description |
|------|-------------|
| `--force` | Overwrite existing config file |
| `--dry-run` | Preview without writing |

### dvt emulator apply

```bash
dvt emulator apply -f emulator.yaml
dvt emulator apply -f emulator.yaml --dry-run
```

| Flag | Short | Description |
|------|-------|-------------|
| `--filename` | `-f` | Path to YAML file (required) |
| `--dry-run` | | Preview without writing to database |

### dvt emulator library list

```bash
dvt emulator library list
dvt emulator library list --type wezterm
dvt emulator library list -o yaml
```

| Flag | Description |
|------|-------------|
| `--output` / `-o` | Output format: `table`, `yaml`, `json` |
| `--type` | Filter by emulator type |

### dvt emulator library show

```bash
dvt emulator library show wezterm-minimal
dvt emulator library show wezterm-minimal -o yaml
```

| Flag | Short | Description |
|------|-------|-------------|
| `--output` | `-o` | Output format: `yaml`, `json` |

---

## dvt wezterm

WezTerm-specific commands for preset-based Lua config generation.

### dvt wezterm list

List available WezTerm presets.

```bash
dvt wezterm list
```

### dvt wezterm show

Show a preset's full definition.

```bash
dvt wezterm show minimal
dvt wezterm show tmux-style
```

### dvt wezterm generate

Generate `wezterm.lua` from a preset without writing to disk (prints to stdout).

```bash
dvt wezterm generate minimal
```

### dvt wezterm apply

Generate and write `wezterm.lua` for a preset.

```bash
dvt wezterm apply minimal
dvt wezterm apply tmux-style -o ~/.config/wezterm/wezterm.lua
```

| Flag | Short | Description |
|------|-------|-------------|
| `--output` | `-o` | Output file path |
