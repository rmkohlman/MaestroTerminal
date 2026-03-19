# Shell Configuration

`dvt` manages shell configurations as `TerminalShell` resources. A shell configuration groups related aliases, environment variables, functions, keybindings, and history settings into a named, reusable resource.

---

## What are Shell Configurations?

Shell configurations capture the customizations you typically scatter across `.zshrc`, `.bashrc`, or separate script files. By managing them as YAML resources, you can version, share, and apply shell settings consistently.

A `TerminalShell` resource can define:

- **Aliases** - Shorthand commands
- **Environment variables** - `export` statements
- **Functions** - Shell function definitions
- **Keybindings** - readline/zle key bindings
- **History settings** - History file size, deduplication, sharing

---

## Commands

### Apply a shell config from YAML

```bash
dvt shell apply -f shell.yaml
```

### List shell configurations

```bash
dvt shell list
dvt shell list -o yaml
dvt shell list -o json
```

### Get a specific shell configuration

```bash
dvt shell get my-shell
dvt shell get my-shell -o yaml
```

### Generate shell config output

Generate the shell script output for a saved configuration:

```bash
dvt shell generate my-shell
```

The generated output is a shell script suitable for sourcing from `.zshrc` or `.bashrc`.

---

## YAML Format

```yaml
apiVersion: devopsmaestro.io/v1
kind: TerminalShell
metadata:
  name: developer-shell
  description: Development shell configuration
  category: developer
spec:
  aliases:
    ll: "ls -lah"
    gs: "git status"
    gc: "git commit"
    gp: "git push"
    k: "kubectl"
  env:
    EDITOR: "nvim"
    GOPATH: "$HOME/go"
    PATH: "$GOPATH/bin:$PATH"
  functions:
    mkcd: |
      function mkcd() {
        mkdir -p "$1" && cd "$1"
      }
    gitlog: |
      function gitlog() {
        git log --oneline --graph --decorate -${1:-20}
      }
  keybindings:
    - key: "^R"
      command: "history-incremental-search-backward"
    - key: "^[[A"
      command: "up-line-or-search"
  history:
    size: 10000
    fileSize: 20000
    ignoreDups: true
    share: true
```

---

## Related

- [Shell Plugins](plugins.md) - Manage zsh plugins
- [Terminal Profiles](../terminal/profiles.md) - Bundle shell config with prompts and plugins
