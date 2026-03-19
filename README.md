# MaestroTerminal

Terminal configuration management library for the [DevOpsMaestro](https://github.com/rmkohlman/devopsmaestro) ecosystem.

## Overview

MaestroTerminal provides a Go library for managing terminal environment configurations as portable YAML resources. It covers the full terminal stack: shell prompts (Starship), shell plugins (zsh), terminal emulators (WezTerm, Alacritty, iTerm2, Kitty), shell profiles, and terminal packages.

Key capabilities:

- **Prompt management** — Define and generate Starship prompt configurations with theme-aware color variables
- **Prompt composition** — Compose modular powerline-style prompts from styles and extensions
- **Plugin management** — Manage zsh plugins across zinit, oh-my-zsh, antigen, sheldon, and manual managers
- **Shell configuration** — Define aliases, environment variables, functions, keybindings, and history settings
- **Profile generation** — Aggregate prompt, plugins, and shell config into complete, ready-to-use config files
- **Terminal emulator config** — Model and parse emulator configurations for WezTerm, Alacritty, iTerm2, and Kitty
- **WezTerm Lua generation** — Generate WezTerm `wezterm.lua` from YAML definitions using Go templates
- **Package bundles** — Bundle prompt, plugin, emulator, and shell settings into reusable terminal packages
- **Built-in libraries** — Embedded curated libraries of prompts, plugins, styles, extensions, emulator configs, and WezTerm presets

## Installation

```bash
go get github.com/rmkohlman/MaestroTerminal
```

## Quick Start

### Generate a complete terminal profile

```go
import (
    "github.com/rmkohlman/MaestroTerminal/terminalops"
    "github.com/rmkohlman/MaestroTerminal/terminalops/profile"
)

// Create manager (uses ~/.dvt as config dir)
mgr, err := terminalops.New()
if err != nil {
    log.Fatal(err)
}

// Install the default preset (populates ~/.dvt with prompts, plugins, shells, profiles)
if err := mgr.InstallPreset("default"); err != nil {
    log.Fatal(err)
}

// Generate config files for a profile
p, err := mgr.ProfileStore().Get("default")
if err != nil {
    log.Fatal(err)
}
if err := mgr.GenerateProfileToDir(p, "~/.config/terminal"); err != nil {
    log.Fatal(err)
}
// Writes ~/.config/terminal/starship.toml and ~/.config/terminal/.zshrc.dvt
```

### Build a Starship prompt config directly

```go
import (
    "github.com/rmkohlman/MaestroTerminal/terminalops/prompt"
)

p := prompt.NewTerminalPrompt("my-prompt")
p.Prompt.Format = "$directory$git_branch$character"
p.Prompt.Modules = map[string]prompt.ModuleConfig{
    "directory": {Format: "[$path]($style) ", Style: "${theme.primary}"},
    "git_branch": {Symbol: " ", Format: "[$symbol$branch]($style) ", Style: "${theme.accent}"},
}

gen := prompt.NewStarshipGenerator()
toml, err := gen.Generate(p.Prompt)
```

### Compose a modular powerline prompt

```go
import (
    "github.com/rmkohlman/MaestroTerminal/terminalops/prompt/composer"
    "github.com/rmkohlman/MaestroTerminal/terminalops/prompt/style"
    "github.com/rmkohlman/MaestroTerminal/terminalops/prompt/extension"
)

styleLib := style.NewLibrary()
extLib := extension.NewLibrary()

promptStyle, _ := styleLib.Get("powerline")
ext, _ := extLib.Get("git")

c := composer.NewStarshipComposer()
composed, err := c.Compose(promptStyle, []*extension.PromptExtension{ext})
```

## Package Overview

| Package | Import Path | Description |
|---------|-------------|-------------|
| `terminalops` | `github.com/rmkohlman/MaestroTerminal/terminalops` | Root manager orchestrating all operations |
| `terminalops/prompt` | `.../terminalops/prompt` | Starship prompt types, parsing, and generation |
| `terminalops/prompt/style` | `.../terminalops/prompt/style` | Modular prompt style definitions |
| `terminalops/prompt/extension` | `.../terminalops/prompt/extension` | Prompt segment extensions |
| `terminalops/prompt/composer` | `.../terminalops/prompt/composer` | Powerline prompt composition |
| `terminalops/plugin` | `.../terminalops/plugin` | zsh plugin types, generation, and library |
| `terminalops/shell` | `.../terminalops/shell` | Shell config types and generation |
| `terminalops/profile` | `.../terminalops/profile` | Complete terminal profile generation |
| `terminalops/package` | `.../terminalops/package` | Terminal package bundles |
| `terminalops/emulator` | `.../terminalops/emulator` | Terminal emulator config types |
| `terminalops/wezterm` | `.../terminalops/wezterm` | WezTerm Lua config generation |

## Dependencies

- [MaestroSDK](https://github.com/rmkohlman/MaestroSDK) — shared paths and resource utilities
- [MaestroPalette](https://github.com/rmkohlman/MaestroPalette) — color palette primitives for prompt theme resolution

## Documentation

For comprehensive documentation, see the [MaestroTerminal Documentation](https://rmkohlman.github.io/MaestroTerminal/).

## License

GPL-3.0 with commercial dual-license. See [LICENSE](LICENSE) for details.

## Part of the DevOpsMaestro Ecosystem

MaestroTerminal is part of the [DevOpsMaestro](https://github.com/rmkohlman/devopsmaestro) toolkit — a kubectl-style CLI for managing containerized development environments.
