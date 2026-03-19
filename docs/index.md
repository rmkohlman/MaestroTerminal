# MaestroTerminal

Terminal configuration management library for the [DevOpsMaestro](https://github.com/rmkohlman/devopsmaestro) ecosystem.

## Overview

MaestroTerminal is a Go library for managing the complete terminal environment as portable YAML resources. It covers every layer of the terminal stack:

| Layer | Package | Description |
|-------|---------|-------------|
| Prompt | `terminalops/prompt` | Starship prompt config generation with theme-aware variables |
| Prompt Styles | `terminalops/prompt/style` | Modular powerline segment layout definitions |
| Prompt Extensions | `terminalops/prompt/extension` | Reusable segment content modules |
| Prompt Composer | `terminalops/prompt/composer` | Assembles styles and extensions into a complete prompt |
| Plugins | `terminalops/plugin` | zsh plugin definitions and install script generation |
| Shell | `terminalops/shell` | Aliases, env vars, functions, history, keybindings |
| Profiles | `terminalops/profile` | Aggregates prompt + plugins + shell into complete configs |
| Packages | `terminalops/package` | Bundles of prompt + plugins + emulator + shell settings |
| Emulators | `terminalops/emulator` | Terminal emulator config types (WezTerm, Alacritty, iTerm2, Kitty) |
| WezTerm | `terminalops/wezterm` | WezTerm Lua config generation from YAML |
| Manager | `terminalops` | Root manager orchestrating all operations |

## Installation

```bash
go get github.com/rmkohlman/MaestroTerminal
```

**Requirements:**

- Go 1.25.6 or later
- Dependencies are fetched automatically via `go get`

### Dependencies

| Module | Purpose |
|--------|---------|
| `github.com/rmkohlman/MaestroSDK` | Shared paths and resource utilities |
| `github.com/rmkohlman/MaestroPalette` | Color palette primitives for prompt theme resolution |
| `github.com/pelletier/go-toml/v2` | TOML encoding for Starship config output |
| `gopkg.in/yaml.v3` | YAML parsing for all resource types |

## Quick Start

### Use the Manager

The `terminalops.Manager` is the recommended entry point. It wires together file-backed stores and generators for all resource types.

```go
import "github.com/rmkohlman/MaestroTerminal/terminalops"

mgr, err := terminalops.New()
if err != nil {
    log.Fatal(err)
}

// Install the default preset into ~/.dvt
if err := mgr.InstallPreset("default"); err != nil {
    log.Fatal(err)
}

// Retrieve and generate a profile
p, err := mgr.ProfileStore().Get("default")
if err != nil {
    log.Fatal(err)
}
if err := mgr.GenerateProfileToDir(p, "/home/user/.config/terminal"); err != nil {
    log.Fatal(err)
}
```

### Generate a Starship Prompt Directly

```go
import "github.com/rmkohlman/MaestroTerminal/terminalops/prompt"

p := prompt.NewTerminalPrompt("my-prompt")
p.Prompt.Format = "$directory$git_branch$character"
p.Prompt.Modules = map[string]prompt.ModuleConfig{
    "directory": {
        Format: "[$path]($style) ",
        Style:  "${theme.primary}",
    },
}

gen := prompt.NewStarshipGenerator()
toml, err := gen.Generate(p.Prompt)
```

### Compose a Modular Powerline Prompt

```go
import (
    "github.com/rmkohlman/MaestroTerminal/terminalops/prompt/composer"
    "github.com/rmkohlman/MaestroTerminal/terminalops/prompt/style"
    "github.com/rmkohlman/MaestroTerminal/terminalops/prompt/extension"
)

styleLib := style.NewLibrary()
extLib := extension.NewLibrary()

promptStyle, _ := styleLib.Get("powerline")
gitExt, _ := extLib.Get("git")

c := composer.NewStarshipComposer()
composed, err := c.Compose(promptStyle, []*extension.PromptExtension{gitExt})
```

## Architecture

MaestroTerminal follows a layered architecture:

```
terminalops.Manager
  ├── PromptStore     (FilePromptStore)       → terminalops/prompt
  ├── PluginStore     (FilePluginStore)       → terminalops/plugin
  ├── ShellStore      (FileShellStore)        → terminalops/shell
  ├── ProfileStore    (FileProfileStore)      → terminalops/profile
  ├── PromptGenerator (StarshipGenerator)     → terminalops/prompt
  ├── PluginGenerator (ZshGenerator)          → terminalops/plugin
  ├── ShellGenerator  (shell.Generator)       → terminalops/shell
  └── ProfileGenerator (profile.Generator)   → terminalops/profile
```

Each package is independently usable. You do not need the `Manager` to use individual generators or parsers.

## Resource YAML Format

All resource types follow a consistent YAML envelope:

```yaml
apiVersion: devopsmaestro.io/v1
kind: <ResourceKind>
metadata:
  name: <name>
  description: <description>
  category: <category>
  labels:
    key: value
spec:
  # resource-specific fields
```

**Resource kinds:**

| Kind | Package |
|------|---------|
| `TerminalPrompt` | `terminalops/prompt` |
| `PromptStyle` | `terminalops/prompt/style` |
| `PromptExtension` | `terminalops/prompt/extension` |
| `TerminalPlugin` | `terminalops/plugin` |
| `TerminalShell` | `terminalops/shell` |
| `TerminalProfile` | `terminalops/profile` |
| `TerminalPackage` | `terminalops/package` |
| `TerminalEmulator` | `terminalops/emulator` |
| `WeztermConfig` | `terminalops/wezterm` (uses `devopsmaestro.dev/v1alpha1`) |

## Presets

The `Manager.InstallPreset(name string)` method populates the config directory with a curated set of resources.

| Preset | Description |
|--------|-------------|
| `"default"` | Balanced setup with Starship prompt, essential plugins, zsh defaults |
| `"minimal"` | Minimal setup for low-overhead environments |
| `"power-user"` | Feature-rich setup with extended plugins and prompt modules |

## Further Reading

- [Manager](manager.md) — root package API
- [Prompts](prompts.md) — Starship prompt config
- [Prompt Styles](prompt-styles.md) — modular segment layouts
- [Prompt Extensions](prompt-extensions.md) — segment content modules
- [Prompt Composer](prompt-composer.md) — assembling complete prompts
- [Plugins](plugins.md) — zsh plugin management
- [Shell](shell.md) — shell configuration
- [Profiles](profiles.md) — complete profile generation
- [Packages](packages.md) — terminal package bundles
- [Emulators](emulators.md) — emulator config types
- [WezTerm](wezterm.md) — WezTerm Lua generation
- [API Reference](api-reference.md) — complete API for all packages
