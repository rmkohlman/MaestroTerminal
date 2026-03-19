# WezTerm

**Import:** `github.com/rmkohlman/MaestroTerminal/terminalops/wezterm`

The `wezterm` package provides types, a Lua config generator, a YAML parser, a store interface, and an embedded library for WezTerm configuration resources. It generates WezTerm's `wezterm.lua` from declarative YAML definitions using Go templates.

## Types

### WezTerm

```go
type WezTerm struct {
    Font       FontConfig     // font configuration
    Window     WindowConfig   // window appearance
    Colors     ColorConfig    // color scheme
    Leader     *LeaderKey     // leader key binding
    Keybindings []Keybinding  // additional key bindings
    KeyTables  map[string][]Keybinding // named key tables
    TabBar     *TabBarConfig  // tab bar configuration
    Pane       *PaneConfig    // pane behavior
    Plugins    []PluginConfig // WezTerm plugin configurations
}
```

### FontConfig

```go
type FontConfig struct {
    Family  string  // font family name
    Size    float64 // font size in points
}
```

Default values (from `NewWezTerm`):
- `Family = "MesloLGS Nerd Font Mono"`
- `Size = 14`

### WindowConfig

```go
type WindowConfig struct {
    Opacity float64 // background opacity (0.0 - 1.0)
}
```

Default: `Opacity = 1.0`

### ColorConfig

```go
type ColorConfig struct {
    Scheme string // color scheme name (e.g., "Catppuccin Mocha")
}
```

### LeaderKey

```go
type LeaderKey struct {
    Key     string // key character
    Mods    string // modifier keys (e.g., "CTRL")
    Timeout int    // timeout in milliseconds
}
```

### Keybinding

```go
type Keybinding struct {
    Key    string // key character or name
    Mods   string // modifier keys
    Action string // WezTerm action (Lua expression)
}
```

### TabBarConfig

```go
type TabBarConfig struct {
    Enabled  bool   // show/hide tab bar
    Position string // "top" or "bottom"
}
```

### PaneConfig

```go
type PaneConfig struct {
    SplitDirection string // default split direction
}
```

### PluginConfig

```go
type PluginConfig struct {
    Name   string         // plugin name
    URL    string         // plugin repository URL
    Config map[string]any // plugin-specific configuration
}
```

### WeztermYAML

`WeztermYAML` is the YAML resource wrapper for `WezTerm`. Use `NewWezTerm` to construct one.

## YAML Format

Note: WezTerm configs use a different `apiVersion` than other resource types.

```yaml
apiVersion: devopsmaestro.dev/v1alpha1
kind: WeztermConfig
metadata:
  name: my-wezterm
  description: My WezTerm configuration
  category: developer
spec:
  font:
    family: "MesloLGS Nerd Font Mono"
    size: 14
  window:
    opacity: 0.95
  colors:
    scheme: "Catppuccin Mocha"
  leader:
    key: "a"
    mods: "CTRL"
    timeout: 1000
  keybindings:
    - key: "|"
      mods: "LEADER"
      action: "wezterm.action.SplitHorizontal{domain='CurrentPaneDomain'}"
    - key: "-"
      mods: "LEADER"
      action: "wezterm.action.SplitVertical{domain='CurrentPaneDomain'}"
  tabBar:
    enabled: true
    position: top
```

## Constructor

### NewWezTerm

```go
func NewWezTerm(name string) *WezTerm
```

Creates a new `WezTerm` with defaults:
- `Font.Family = "MesloLGS Nerd Font Mono"`
- `Font.Size = 14`
- `Window.Opacity = 1.0`

## Helper Methods

```go
func (w *WezTerm) HasColors() bool
func (w *WezTerm) HasThemeRef() bool
func (w *WezTerm) HasLeader() bool
func (w *WezTerm) HasKeybindings() bool
func (w *WezTerm) HasKeyTables() bool
func (w *WezTerm) HasPlugins() bool
```

These predicate methods are used by the Lua template generator to conditionally include configuration sections.

## Generator

### LuaGenerator

```go
type LuaGenerator struct {
    // unexported fields
}
```

Generates WezTerm's `wezterm.lua` from a `WezTerm` config using Go's `text/template`.

### Generator Interface

```go
type Generator interface {
    Generate(spec *WeztermSpec) (string, error)
    GenerateFromConfig(config *WezTerm) (string, error)
}
```

### NewLuaGenerator

```go
func NewLuaGenerator() *LuaGenerator
```

### Methods

```go
func (g *LuaGenerator) Generate(spec *WeztermSpec) (string, error)
func (g *LuaGenerator) GenerateFromConfig(config *WezTerm) (string, error)
```

| Method | Description |
|--------|-------------|
| `Generate` | Generates Lua from a `WeztermSpec` (used internally by the resource system) |
| `GenerateFromConfig` | Generates Lua directly from a `WezTerm` struct |

```go
gen := wezterm.NewLuaGenerator()

w := wezterm.NewWezTerm("my-config")
w.Colors.Scheme = "Catppuccin Mocha"
w.Window.Opacity = 0.95
w.Leader = &wezterm.LeaderKey{Key: "a", Mods: "CTRL", Timeout: 1000}

lua, err := gen.GenerateFromConfig(w)
if err != nil {
    log.Fatal(err)
}
os.WriteFile("wezterm.lua", []byte(lua), 0644)
```

## Parser

### Parser Interface

```go
type Parser interface {
    Parse(data []byte) (*WezTerm, error)
    ParseYAML(data []byte) (*WeztermYAML, error)
}
```

### NewParser

```go
func NewParser() Parser
```

Returns a `YAMLParser` implementation.

```go
parser := wezterm.NewParser()

data, _ := os.ReadFile("wezterm.yaml")
w, err := parser.Parse(data)
```

## Store Interface

```go
type Store interface {
    Get(name string) (*WezTerm, error)
    List() ([]*WezTerm, error)
    Save(w *WezTerm) error
    Delete(name string) error
    Exists(name string) bool
}
```

## Embedded Library

**Import:** `github.com/rmkohlman/MaestroTerminal/terminalops/wezterm/library`

### WeztermLibrary

```go
type WeztermLibrary struct {
    // unexported fields
}
```

Implements the `Library` interface:

```go
type Library interface {
    Get(name string) (*wezterm.WezTerm, error)
    List() ([]*wezterm.WezTerm, error)
    ListByCategory(category string) ([]*wezterm.WezTerm, error)
    Names() []string
    Categories() []string
    Count() int
}
```

### NewWeztermLibrary

```go
func NewWeztermLibrary() (*WeztermLibrary, error)
```

Loads the embedded preset library.

### Embedded Presets

| Name | Description |
|------|-------------|
| `default` | Default WezTerm configuration |
| `minimal` | Minimal WezTerm configuration |
| `tmux-style` | WezTerm configured with tmux-style keybindings |

### Usage

```go
import weztermlib "github.com/rmkohlman/MaestroTerminal/terminalops/wezterm/library"

lib, err := weztermlib.NewWeztermLibrary()
if err != nil {
    log.Fatal(err)
}

w, err := lib.Get("tmux-style")
if err != nil {
    log.Fatal(err)
}

gen := wezterm.NewLuaGenerator()
lua, err := gen.GenerateFromConfig(w)
```
