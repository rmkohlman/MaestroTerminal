# MaestroTerminal - Terminal Configuration Manager

Terminal configuration management via the `dvt` CLI, part of the [DevOpsMaestro](https://github.com/rmkohlman/devopsmaestro) toolkit.

## Overview

`dvt` manages your entire terminal environment as declarative YAML resources. It covers every layer of the terminal stack: shell prompts (Starship, Powerlevel10k, Oh-My-Posh), shell plugins (zsh), shell configurations, terminal profiles, terminal emulators (WezTerm, Alacritty, Kitty, iTerm2), and terminal packages.

## Key Features

- **Prompt management** - Define and generate Starship, Powerlevel10k, and Oh-My-Posh configurations from YAML
- **Theme-aware colors** - Use `${theme.color}` variables in prompt styles that resolve against the active palette
- **Shell plugin management** - Manage zsh plugins across zinit, oh-my-zsh, antigen, sheldon, and manual managers
- **Shell configuration** - Define aliases, environment variables, functions, keybindings, and history settings
- **Terminal profiles** - Aggregate prompt, plugins, and shell config into complete, ready-to-use config files
- **Terminal emulators** - Model and apply configurations for WezTerm, Alacritty, Kitty, and iTerm2
- **WezTerm Lua generation** - Generate `wezterm.lua` from YAML definitions using built-in presets
- **Terminal packages** - Bundle prompt, plugin, emulator, and shell settings into reusable packages
- **Built-in libraries** - Curated libraries of prompts, plugins, emulator configs, and WezTerm presets

## Quick Install

```bash
brew tap rmkohlman/tap
brew install devopsmaestro
dvt version
```

The `devopsmaestro` Homebrew package includes `dvm`, `nvp`, and `dvt`.

## Quick Start

```bash
# Initialize dvt config directory
dvt init

# Browse the built-in prompt library
dvt prompt library list

# Install a prompt from the library
dvt prompt library install starship-minimal

# Generate starship.toml for a prompt
dvt prompt generate starship-minimal

# Set a prompt as active
dvt prompt set starship-minimal

# Apply a WezTerm preset
dvt wezterm apply minimal
```

## Documentation

Full documentation is available at the [MaestroTerminal Documentation](https://rmkohlman.github.io/MaestroTerminal/).

## Part of the DevOpsMaestro Ecosystem

`dvt` is part of the [DevOpsMaestro](https://github.com/rmkohlman/devopsmaestro) toolkit alongside `dvm` (workspace management) and `nvp` (Neovim plugin manager).

## License

GPL-3.0 with commercial dual-license. See [LICENSE](LICENSE) for details.
