# MaestroTerminal

Terminal configuration management library for the [DevOpsMaestro](https://github.com/rmkohlman/devopsmaestro) ecosystem.

## Overview

MaestroTerminal provides tools for managing terminal configurations including shell prompts (Starship), shell plugins (zsh), terminal emulators (WezTerm, Alacritty, iTerm2, Kitty), shell profiles, and terminal packages — all as portable YAML resources.

## Installation

```bash
go get github.com/rmkohlman/MaestroTerminal@latest
```

## Packages

| Package | Description |
|---------|-------------|
| `terminalops` | Root manager — orchestrates all terminal operations |
| `terminalops/prompt` | Prompt types (Starship), YAML parsing, config generation, rendering |
| `terminalops/prompt/composer` | Starship format composition and transition animations |
| `terminalops/prompt/extension` | Prompt extension types and library |
| `terminalops/prompt/style` | Prompt style types and library |
| `terminalops/plugin` | Shell plugin types (zsh), YAML parsing, install script generation |
| `terminalops/shell` | Shell config types (aliases, env vars, functions), YAML parsing |
| `terminalops/profile` | Aggregates prompt + plugins + shell into complete configs |
| `terminalops/package` | Terminal package types — bundles of prompt + plugins + emulator |
| `terminalops/emulator` | Terminal emulator config types and YAML parsing |
| `terminalops/wezterm` | WezTerm-specific config generation and presets |

## Usage

```go
import "github.com/rmkohlman/MaestroTerminal/terminalops"

// Create a manager with default file storage
mgr, err := terminalops.New()
if err != nil {
    log.Fatal(err)
}

// Install a preset profile
err = mgr.InstallPreset("default")

// Generate config files
err = mgr.GenerateProfile("default", "~/.config")
```

## Dependencies

- [MaestroSDK](https://github.com/rmkohlman/MaestroSDK) — shared paths and resource utilities
- [MaestroPalette](https://github.com/rmkohlman/MaestroPalette) — color palette primitives for prompt rendering

## License

See [DevOpsMaestro](https://github.com/rmkohlman/devopsmaestro) for license information.
