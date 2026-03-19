# Prompt Library

`dvt` includes a built-in library of curated prompt configurations ready to install. Library prompts cover a range of styles, from minimal single-line prompts to feature-rich powerline layouts.

---

## Browsing the Library

List all available prompts:

```bash
dvt prompt library list
```

Example output:

```
NAME                  TYPE        CATEGORY    DESCRIPTION
starship-minimal      starship    minimal     Clean single-line Starship prompt
starship-powerline    starship    powerline   Powerline-style Starship with git info
starship-workspace    starship    developer   Starship with dvm workspace context
p10k-lean             powerlevel10k  minimal  Lean Powerlevel10k layout
p10k-rainbow          powerlevel10k  powerline Rainbow Powerlevel10k layout
```

### Filter by category

```bash
dvt prompt library list -c minimal
dvt prompt library list -c powerline
dvt prompt library list -c developer
```

### Output formats

```bash
dvt prompt library list -o yaml
dvt prompt library list -o json
```

### List available categories

```bash
dvt prompt library categories
```

---

## Viewing a Library Prompt

Show the full YAML definition for a library prompt:

```bash
dvt prompt library show starship-minimal
```

Output as JSON:

```bash
dvt prompt library show starship-minimal -o json
```

---

## Installing Library Prompts

Install a single prompt from the library into your local database:

```bash
dvt prompt library install starship-minimal
```

Install multiple prompts at once:

```bash
dvt prompt library install starship-minimal starship-powerline p10k-lean
```

Install all library prompts:

```bash
dvt prompt library install --all
```

After installation, the prompt is available via `dvt prompt list` and can be generated or set as active:

```bash
dvt prompt generate starship-minimal
dvt prompt set starship-minimal
```

---

## After Installing

Once installed, use standard prompt commands:

```bash
# Verify it was installed
dvt prompt list

# View the installed prompt
dvt prompt get starship-minimal -o yaml

# Generate the config file
dvt prompt generate starship-minimal

# Set as active
dvt prompt set starship-minimal
```

---

## Related

- [Prompts Overview](overview.md) - Full prompt command reference
- [Theme Integration](theme-integration.md) - How library prompts use theme variables
