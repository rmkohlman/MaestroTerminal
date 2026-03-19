# TerminalPackage YAML Reference

`TerminalPackage` bundles a prompt, plugins, emulator settings, and theme preferences into a single installable resource. Packages support single inheritance via the `extends` field.

---

## Full Example

```yaml
apiVersion: devopsmaestro.io/v1
kind: TerminalPackage
metadata:
  name: developer                              # Unique name (required)
  description: Developer-focused terminal package
  category: work                               # Category for filtering
  tags:                                        # Optional tags
    - dev
    - git
    - productivity
spec:
  extends: core                                # Inherit from another package
  plugins:                                     # Plugin names to include
    - zsh-autosuggestions
    - zsh-syntax-highlighting
    - fzf
    - zsh-z
  prompts:                                     # Prompt names to include
    - starship-powerline
  profiles:                                    # Profile names to include
    - developer-profile
  promptStyle: powerline                       # Prompt style name
  promptExtensions:                            # Prompt extension names
    - git
    - directory
    - time
    - node
  theme: catppuccin-mocha                      # Theme name
  wezterm:                                     # WezTerm-specific overrides
    fontSize: 14
    colorScheme: "Catppuccin Mocha"
    fontFamily: "MesloLGS Nerd Font Mono"
```

---

## Field Reference

### metadata fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Unique identifier for this package |
| `description` | string | No | Human-readable description |
| `category` | string | No | Category for filtering (`dvt package list -c`) |
| `tags` | list | No | Arbitrary tags for filtering |

### spec fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `extends` | string | No | Name of another package to inherit from (single inheritance) |
| `plugins` | list | No | Names of `TerminalPlugin` resources to include |
| `prompts` | list | No | Names of `TerminalPrompt` resources to include |
| `profiles` | list | No | Names of `TerminalProfile` resources to include |
| `promptStyle` | string | No | Name of a prompt style to apply |
| `promptExtensions` | list | No | Names of prompt extensions to apply |
| `theme` | string | No | Theme name to associate with this package |
| `wezterm` | object | No | WezTerm-specific configuration overrides |

### wezterm object fields

| Field | Type | Description |
|-------|------|-------------|
| `fontSize` | int | WezTerm font size in points |
| `colorScheme` | string | WezTerm color scheme name |
| `fontFamily` | string | WezTerm font family name |

---

## Inheritance with extends

A package can extend one other package, inheriting its plugins, prompts, profiles, and theme. Child fields override inherited values.

```yaml
# Base package
apiVersion: devopsmaestro.io/v1
kind: TerminalPackage
metadata:
  name: core
spec:
  plugins:
    - zsh-autosuggestions
    - zsh-syntax-highlighting
  prompts:
    - starship-minimal
  theme: catppuccin-latte

---
# Derived package
apiVersion: devopsmaestro.io/v1
kind: TerminalPackage
metadata:
  name: developer
spec:
  extends: core                 # Inherits plugins and prompts from core
  plugins:
    - fzf                       # Added on top of core's plugins
    - zsh-z
  prompts:
    - starship-powerline        # Overrides core's starship-minimal
  theme: catppuccin-mocha       # Overrides core's theme
```

---

## String or List Shorthand

Plugin, prompt, profile, and extension fields accept either a single string or a list:

```yaml
# Single string
plugins: zsh-autosuggestions

# List
plugins:
  - zsh-autosuggestions
  - fzf
```

---

## Usage Examples

### Minimal package

```yaml
apiVersion: devopsmaestro.io/v1
kind: TerminalPackage
metadata:
  name: minimal
  description: Minimal terminal setup
  category: minimal
spec:
  plugins:
    - zsh-autosuggestions
  prompts:
    - starship-minimal
```

### Full developer package with WezTerm

```yaml
apiVersion: devopsmaestro.io/v1
kind: TerminalPackage
metadata:
  name: full-dev
  description: Complete developer terminal with WezTerm
  category: developer
  tags:
    - dev
    - wezterm
    - git
spec:
  extends: core
  plugins:
    - zsh-autosuggestions
    - zsh-syntax-highlighting
    - fzf
    - zsh-z
    - zsh-completions
  prompts:
    - starship-powerline
  promptExtensions:
    - git
    - directory
    - node
    - rust
  theme: catppuccin-mocha
  wezterm:
    fontSize: 13
    colorScheme: "Catppuccin Mocha"
    fontFamily: "JetBrains Mono Nerd Font"
```

### Install and use a package

```bash
# Install from the built-in library
dvt package install developer

# Or apply a custom package from YAML (via emulator/profile apply commands)
# Then list to confirm
dvt package list
dvt package get developer -o yaml
```

---

## Built-in Library Packages

| Name | Extends | Description |
|------|---------|-------------|
| `core` | (none) | Base package with essential plugins and minimal prompt |
| `developer` | `core` | Developer-focused with git tools and powerline prompt |
| `maestro` | `developer` | Full DevOpsMaestro setup with workspace context prompt |

---

## Related

- [Terminal Packages](../terminal/packages.md) - Package management commands
- [Terminal Profiles](../terminal/profiles.md) - Profile resources
- [TerminalPrompt YAML Reference](terminal-prompt.md) - Prompt resource schema
