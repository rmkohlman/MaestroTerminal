# Terminal Packages

A terminal package is a bundle that groups together a prompt, plugins, emulator configuration, and shell settings under a single named resource. Packages support inheritance, allowing you to build layered configurations from a base package.

---

## What are Terminal Packages?

Where a terminal profile focuses on generating config files (prompt + plugins + shell), a terminal package is a broader bundle that also includes emulator settings and theme preferences. Packages can extend other packages via the `extends` field.

Packages stored in the built-in library represent complete, opinionated terminal setups ready to install.

---

## Commands

### List packages

```bash
dvt package list
```

Filter by category:

```bash
dvt package list -c developer
dvt package list -c minimal
```

Show extended information:

```bash
dvt package list -w
```

Show only library packages (not installed):

```bash
dvt package list --library
```

Show only user-installed packages:

```bash
dvt package list --user
```

Output as YAML or JSON:

```bash
dvt package list -o yaml
dvt package list -o json
```

The `pkg` alias works for all package subcommands:

```bash
dvt pkg list
```

### Get a specific package

```bash
dvt package get developer
dvt package get developer -o yaml
```

### Install a package

Install a package from the built-in library:

```bash
dvt package install developer
```

Preview what would be installed without making changes:

```bash
dvt package install developer --dry-run
```

---

## Built-in Library Packages

| Name | Description |
|------|-------------|
| `core` | Base package with essential plugins and minimal Starship prompt |
| `developer` | Developer-focused package extending core, with git tools and powerline prompt |
| `maestro` | Full DevOpsMaestro package with workspace context prompt and complete tooling |

---

## YAML Format

```yaml
apiVersion: devopsmaestro.io/v1
kind: TerminalPackage
metadata:
  name: developer
  description: Developer-focused terminal package
  category: work
  tags:
    - dev
    - git
spec:
  extends: core
  plugins:
    - zsh-autosuggestions
    - zsh-syntax-highlighting
    - fzf
  prompts:
    - starship-powerline
  promptStyle: powerline
  promptExtensions:
    - git
    - directory
    - time
  theme: catppuccin-mocha
  wezterm:
    fontSize: 14
    colorScheme: "Catppuccin Mocha"
    fontFamily: "MesloLGS Nerd Font Mono"
```

## Field Reference

| Field | Type | Description |
|-------|------|-------------|
| `extends` | string | Name of another package to inherit from |
| `plugins` | list | Plugin names to include |
| `prompts` | list | Prompt names to include |
| `profiles` | list | Profile names to include |
| `promptStyle` | string | Prompt style name to apply |
| `promptExtensions` | list | Prompt extension names to apply |
| `theme` | string | Theme name |
| `wezterm.fontSize` | int | WezTerm font size |
| `wezterm.colorScheme` | string | WezTerm color scheme name |
| `wezterm.fontFamily` | string | WezTerm font family |

---

## Related

- [Terminal Profiles](profiles.md) - Lighter-weight prompt+plugins+shell bundles
- [Terminal Emulators](emulators.md) - Emulator configuration management
- [TerminalPackage YAML Reference](../reference/terminal-package.md)
