# Quick Start

Get your terminal configured with `dvt` in minutes.

---

## 1. Initialize

Create the `dvt` config directory:

```bash
dvt init
```

---

## 2. Browse the Prompt Library

See available built-in prompts:

```bash
dvt prompt library list
```

Filter by category:

```bash
dvt prompt library list -c minimal
```

View details for a specific prompt:

```bash
dvt prompt library show starship-minimal
```

---

## 3. Install a Prompt

Install a prompt from the library into your local database:

```bash
dvt prompt library install starship-minimal
```

Install multiple prompts at once:

```bash
dvt prompt library install starship-minimal starship-powerline
```

---

## 4. Apply a Prompt from YAML

Create a custom prompt definition:

```yaml
# my-prompt.yaml
apiVersion: devopsmaestro.io/v1
kind: TerminalPrompt
metadata:
  name: my-prompt
  description: My custom Starship prompt
spec:
  type: starship
  addNewline: true
  palette: theme
  format: "$directory$git_branch$character"
  modules:
    directory:
      format: "[$path]($style) "
      style: "${theme.primary}"
    git_branch:
      symbol: " "
      format: "[$symbol$branch]($style) "
      style: "${theme.accent}"
    character:
      successSymbol: "[>](bold ${theme.success})"
      errorSymbol: "[>](bold ${theme.error})"
```

Apply it:

```bash
dvt prompt apply -f my-prompt.yaml
```

---

## 5. Generate a Config File

Generate `starship.toml` from a saved prompt:

```bash
dvt prompt generate my-prompt
```

---

## 6. Set the Active Prompt

Mark a prompt as active:

```bash
dvt prompt set my-prompt
```

---

## 7. Configure WezTerm

Browse built-in WezTerm presets:

```bash
dvt wezterm list
```

Preview a preset:

```bash
dvt wezterm show minimal
```

Apply a preset to generate `wezterm.lua`:

```bash
dvt wezterm apply minimal
```

---

## Next Steps

- [Prompt Overview](../prompts/overview.md) - Learn about all prompt management commands
- [Theme Integration](../prompts/theme-integration.md) - Use theme color variables in prompts
- [Shell Plugins](../shell/plugins.md) - Manage zsh plugins
- [Terminal Profiles](../terminal/profiles.md) - Bundle everything into a deployable profile
- [WezTerm](../terminal/wezterm.md) - Full WezTerm configuration guide
