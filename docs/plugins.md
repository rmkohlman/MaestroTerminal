# Plugins

**Import:** `github.com/rmkohlman/MaestroTerminal/terminalops/plugin`

The `plugin` package provides types, a generator, a parser, a store interface, and an embedded library for zsh shell plugin resources.

## Types

### PluginManager

```go
type PluginManager string
```

**Constants:**

| Constant | Value | Description |
|----------|-------|-------------|
| `PluginManagerZinit` | `"zinit"` | Zinit plugin manager |
| `PluginManagerOhMyZsh` | `"oh-my-zsh"` | Oh My Zsh framework |
| `PluginManagerAntigen` | `"antigen"` | Antigen plugin manager |
| `PluginManagerManual` | `"manual"` | Manual git clone + source |
| `PluginManagerSheldon` | `"sheldon"` | Sheldon plugin manager |

### LoadMode

```go
type LoadMode string
```

**Constants:**

| Constant | Value | Description |
|----------|-------|-------------|
| `LoadModeImmediate` | `"immediate"` | Load immediately at shell startup |
| `LoadModeDeferred` | `"deferred"` | Defer loading until after shell init |
| `LoadModeLazy` | `"lazy"` | Load only when first used |

### Plugin

```go
type Plugin struct {
    Repo          string            // git repository URL or Oh My Zsh plugin name
    Source        string            // source URL override
    Branch        string            // git branch to use
    Tag           string            // git tag to pin
    Manager       PluginManager     // plugin manager to use
    LoadMode      LoadMode          // when to load the plugin
    OhMyZshPlugin string            // Oh My Zsh built-in plugin name
    SourceFiles   []string          // specific files to source
    Config        string            // inline configuration snippet
    Env           map[string]string // environment variables to set before loading
    Dependencies  []string          // other plugin names this plugin depends on
    Priority      int               // load order priority (lower = earlier)
}
```

## Constructor

### NewPlugin

```go
func NewPlugin(name, repo string) *Plugin
```

Creates a new `Plugin` with the given name and repository URL.

```go
p := plugin.NewPlugin("zsh-autosuggestions",
    "zsh-users/zsh-autosuggestions")
p.Manager = plugin.PluginManagerZinit
p.LoadMode = plugin.LoadModeDeferred
```

## Helper Methods

### IsOhMyZshBuiltin

```go
func (p *Plugin) IsOhMyZshBuiltin() bool
```

Returns `true` if `OhMyZshPlugin` is set, indicating this plugin is a built-in Oh My Zsh plugin (not requiring a git clone).

### GetSourceURL

```go
func (p *Plugin) GetSourceURL() string
```

Returns the effective source URL. Returns `Source` if set, otherwise returns `Repo`.

## YAML Format

```yaml
apiVersion: devopsmaestro.io/v1
kind: TerminalPlugin
metadata:
  name: zsh-autosuggestions
  description: Fish-like autosuggestions for zsh
  category: completion
  labels:
    type: completion
spec:
  repo: zsh-users/zsh-autosuggestions
  manager: zinit
  loadMode: deferred
  priority: 10
  env:
    ZSH_AUTOSUGGEST_HIGHLIGHT_STYLE: "fg=#757575"
```

## Generator

### ZshGenerator

```go
type ZshGenerator struct {
    PluginDir string // directory for manually installed plugins
}
```

Default plugin dir: `${HOME}/.local/share/zsh/plugins`

### NewZshGenerator

```go
func NewZshGenerator(pluginDir string) *ZshGenerator
```

Creates a new `ZshGenerator` with the specified plugin directory. Pass an empty string to use the default directory.

### Generate

```go
func (g *ZshGenerator) Generate(plugins []*Plugin) (string, error)
```

Generates a zsh snippet that loads all provided plugins. The generator:

1. Groups plugins by manager
2. Sorts within each group by `Priority` (ascending)
3. Emits the appropriate load syntax for each manager

**Manager support:**

| Manager | Output |
|---------|--------|
| Manual | `git clone` + `source` statements |
| Zinit | `zinit` load/light/snippet directives |
| Oh My Zsh | `plugins=(...)` array or built-in sourcing |
| Antigen | `antigen bundle` directives |
| Sheldon | Sheldon TOML config generation |

```go
gen := plugin.NewZshGenerator("")
zsh, err := gen.Generate(plugins)
if err != nil {
    log.Fatal(err)
}
fmt.Println(zsh)
```

### GenerateInstallScript

```go
func (g *ZshGenerator) GenerateInstallScript(plugins []*Plugin) string
```

Generates a shell install script for all plugins that require external installation (e.g., git clone for manual plugins). Returns a bash-compatible script string.

## Store Interface

### PluginStore

```go
type PluginStore interface {
    Create(p *Plugin) error
    Update(p *Plugin) error
    Upsert(p *Plugin) error
    Delete(name string) error
    Get(name string) (*Plugin, error)
    List() ([]*Plugin, error)
    ListByManager(manager string) ([]*Plugin, error)
    ListByCategory(category string) ([]*Plugin, error)
    Exists(name string) (bool, error)
    Close() error
}
```

## Parser

```go
func ParseYAMLFile(path string) (*Plugin, error)
func ParseYAML(data []byte) (*Plugin, error)
```

## Embedded Library

**Import:** `github.com/rmkohlman/MaestroTerminal/terminalops/plugin/library`

### Library

Provides an in-memory library of curated plugin definitions loaded from embedded YAML files.

**Methods:**

```go
func (l *Library) Get(name string) (*plugin.Plugin, error)
func (l *Library) List() ([]*plugin.Plugin, error)
func (l *Library) ListByManager(manager plugin.PluginManager) ([]*plugin.Plugin, error)
func (l *Library) ListByCategory(category string) ([]*plugin.Plugin, error)
func (l *Library) Exists(name string) bool
func (l *Library) Count() int
func (l *Library) EssentialPlugins() []*plugin.Plugin
```

### EssentialPlugins

```go
func (l *Library) EssentialPlugins() []*plugin.Plugin
```

Returns the minimal set of plugins recommended for all setups:

- `zsh-autosuggestions`
- `zsh-syntax-highlighting`

### Embedded Plugins

| Name | Description |
|------|-------------|
| `fast-syntax-highlighting` | Fast syntax highlighting for zsh |
| `fzf-tab` | fzf-based tab completion |
| `fzf` | General-purpose fuzzy finder |
| `zsh-autosuggestions` | Fish-like autosuggestions |
| `zsh-completions` | Additional zsh completions |
| `zsh-history-substring-search` | History search with up/down arrows |
| `zsh-syntax-highlighting` | Syntax highlighting for the zsh command line |
| `zsh-vi-mode` | Improved vi mode for zsh |

### Usage

```go
import pluginlib "github.com/rmkohlman/MaestroTerminal/terminalops/plugin/library"

lib := pluginlib.NewLibrary()

// Get essential plugins for a minimal setup
essential := lib.EssentialPlugins()

// Get all zinit-managed plugins
zinitPlugins, _ := lib.ListByManager(plugin.PluginManagerZinit)

fmt.Printf("Library contains %d plugins\n", lib.Count())
```
