# Terminal Profiles

A terminal profile is an aggregate resource that bundles a prompt, plugins, and shell configuration into a single deployable unit. Applying a profile generates all the config files needed for a complete terminal setup.

---

## What are Terminal Profiles?

Rather than managing prompt, plugins, and shell config separately, a profile ties them together by name. When you generate a profile, `dvt` produces:

- `starship.toml` (or equivalent for your prompt engine)
- `.zshrc.dvt` (shell config and plugin loading block)

All files are written to the output directory in one step.

---

## Commands

### Apply a profile from YAML

```bash
dvt profile apply -f profile.yaml
```

### List profiles

```bash
dvt profile list
dvt profile list -o yaml
dvt profile list -o json
```

### Get a specific profile

```bash
dvt profile get my-profile
dvt profile get my-profile -o yaml
```

### Generate config files from a profile

Generate all config files for a profile into a directory:

```bash
dvt profile generate my-profile
```

Specify an output directory:

```bash
dvt profile generate my-profile -o ~/.config/terminal
```

Preview what would be generated without writing files:

```bash
dvt profile generate my-profile --dry-run
```

### Activate a profile

Set a profile as active. This applies the profile's settings as the current terminal configuration:

```bash
dvt profile use my-profile
```

---

## Profile Presets

`dvt` includes built-in profile presets for common terminal setups.

### List available presets

```bash
dvt profile preset list
```

### Install a preset

```bash
dvt profile preset install default
dvt profile preset install minimal
dvt profile preset install power-user
```

Installing a preset creates the profile resource and all referenced prompts, plugins, and shell configs in your local database.

---

## YAML Format

```yaml
apiVersion: devopsmaestro.io/v1
kind: TerminalProfile
metadata:
  name: my-profile
  description: My development terminal profile
  category: developer
spec:
  prompt: starship-minimal
  plugins:
    - zsh-autosuggestions
    - zsh-syntax-highlighting
    - fzf
  shell: developer-shell
```

| Field | Type | Description |
|-------|------|-------------|
| `prompt` | string | Name of a saved `TerminalPrompt` resource |
| `plugins` | list | Names of saved `TerminalPlugin` resources |
| `shell` | string | Name of a saved `TerminalShell` resource |

---

## Generated File Structure

When you run `dvt profile generate my-profile -o ~/.config/terminal`, the output directory contains:

```
~/.config/terminal/
├── starship.toml        # Prompt configuration
└── .zshrc.dvt           # Plugin loading + shell configuration
```

Source `.zshrc.dvt` from your `.zshrc` to activate the generated configuration:

```bash
# In ~/.zshrc
source ~/.config/terminal/.zshrc.dvt
eval "$(starship init zsh)"
```

---

## Related

- [Prompts Overview](../prompts/overview.md) - Managing prompt resources
- [Shell Plugins](../shell/plugins.md) - Managing plugin resources
- [Shell Configuration](../shell/configuration.md) - Managing shell config resources
- [Terminal Packages](packages.md) - Broader bundles including emulator config
