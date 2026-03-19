# Installation

## Requirements

- **macOS or Linux**
- **Go 1.21+** (only if building from source)

---

## Homebrew (Recommended)

The `devopsmaestro` Homebrew package includes `dvm`, `nvp`, and `dvt`:

```bash
brew tap rmkohlman/tap
brew install devopsmaestro
```

Verify the installation:

```bash
dvt version
```

---

## From Source

Build `dvt` from the main DevOpsMaestro repository:

```bash
git clone https://github.com/rmkohlman/devopsmaestro.git
cd devopsmaestro
go build -o dvt ./cmd/dvt/
sudo mv dvt /usr/local/bin/
```

Verify the installation:

```bash
dvt version
```

---

## Shell Completion

Enable tab completion for your shell:

=== "Bash"

    ```bash
    # Add to ~/.bashrc
    eval "$(dvt completion bash)"
    ```

=== "Zsh"

    ```bash
    # Add to ~/.zshrc
    eval "$(dvt completion zsh)"
    ```

=== "Fish"

    ```bash
    # Add to ~/.config/fish/config.fish
    dvt completion fish | source
    ```

=== "PowerShell"

    ```powershell
    # Add to your PowerShell profile
    dvt completion powershell | Out-String | Invoke-Expression
    ```

---

## Initialize dvt

After installation, initialize the `dvt` config directory:

```bash
dvt init
```

This creates `~/.dvt/` with the default configuration structure. The shared database at `~/.devopsmaestro/devopsmaestro.db` is used automatically.

!!! note "Config directory"
    The default config directory is `~/.dvt/`. Override it with the `--config` flag on any command.

---

## Next Steps

- [Quick Start](quickstart.md) - Configure your terminal in minutes
- [Prompt Overview](../prompts/overview.md) - Start managing your shell prompt
- [WezTerm](../terminal/wezterm.md) - Generate your WezTerm configuration
