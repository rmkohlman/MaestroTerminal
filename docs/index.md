# MaestroTerminal

Terminal configuration management via the `dvt` CLI, part of the [DevOpsMaestro](https://github.com/rmkohlman/devopsmaestro) toolkit.

---

## What is dvt?

`dvt` manages your complete terminal environment as declarative YAML resources. Define your shell prompt, plugins, configurations, and terminal emulator settings in YAML and apply them consistently across machines.

| Layer | What dvt manages |
|-------|-----------------|
| Prompts | Starship, Powerlevel10k, and Oh-My-Posh configurations |
| Shell Plugins | zsh plugins across zinit, oh-my-zsh, antigen, sheldon, and manual managers |
| Shell Configuration | Aliases, environment variables, functions, keybindings, and history |
| Terminal Profiles | Aggregated prompt + plugins + shell config as a single deployable unit |
| Terminal Emulators | WezTerm, Alacritty, Kitty, and iTerm2 configuration management |
| WezTerm | Lua config generation from YAML with preset library |
| Terminal Packages | Bundles of prompt + plugins + emulator + shell settings |

## Key Features

- **YAML-first** - Every terminal resource is a declarative YAML file with `apiVersion: devopsmaestro.io/v1`
- **Theme-aware colors** - Use `${theme.color}` variables in prompt styles that resolve against the active palette
- **Built-in libraries** - Curated collections of prompts, plugins, WezTerm presets, and packages ready to install
- **kubectl-style commands** - Consistent `apply`, `list`, `get`, `delete` patterns across all resource types
- **Profile generation** - Generate complete config file sets (`starship.toml`, `.zshrc.dvt`) from a single profile resource
- **WezTerm integration** - Generate `wezterm.lua` from YAML with automatic theme color mapping

## Quick Install

=== "Homebrew (Recommended)"

    ```bash
    brew tap rmkohlman/tap
    brew install devopsmaestro
    dvt version
    ```

=== "From Source"

    ```bash
    git clone https://github.com/rmkohlman/devopsmaestro.git
    cd devopsmaestro
    go build -o dvt ./cmd/dvt/
    sudo mv dvt /usr/local/bin/
    dvt version
    ```

## Quick Example

```bash
# Initialize dvt
dvt init

# Browse the built-in prompt library
dvt prompt library list

# Install a prompt from the library
dvt prompt library install starship-minimal

# Generate starship.toml
dvt prompt generate starship-minimal

# Set as active prompt
dvt prompt set starship-minimal

# Apply a WezTerm preset
dvt wezterm apply minimal
```

## Next Steps

<div class="grid cards" markdown>

-   **Installation**

    ---

    Install dvt and configure your shell

    [Installation](getting-started/installation.md)

-   **Quick Start**

    ---

    Get your terminal configured in minutes

    [Quick Start](getting-started/quickstart.md)

-   **Prompt Management**

    ---

    Configure Starship, Powerlevel10k, and Oh-My-Posh

    [Prompts Overview](prompts/overview.md)

-   **WezTerm Integration**

    ---

    Generate wezterm.lua with theme-aware presets

    [WezTerm](terminal/wezterm.md)

-   **Commands Reference**

    ---

    Complete reference for all dvt commands

    [Commands Reference](commands.md)

-   **YAML Reference**

    ---

    Complete YAML schemas for all resource types

    [TerminalPrompt](reference/terminal-prompt.md)

</div>
