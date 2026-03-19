# Emulators

**Import:** `github.com/rmkohlman/MaestroTerminal/terminalops/emulator`

The `emulator` package provides types, a parser, a store interface, and an embedded library for terminal emulator configuration resources.

## Types

### EmulatorType

```go
type EmulatorType string
```

**Constants:**

| Constant | Value | Description |
|----------|-------|-------------|
| `EmulatorTypeWezterm` | `"wezterm"` | WezTerm |
| `EmulatorTypeAlacritty` | `"alacritty"` | Alacritty |
| `EmulatorTypeKitty` | `"kitty"` | Kitty |
| `EmulatorTypeITerm2` | `"iterm2"` | iTerm2 |

### Emulator

```go
type Emulator struct {
    Config    map[string]any    // emulator-specific configuration key-value pairs
    ThemeRef  string            // reference to a theme resource by name
    Workspace string            // workspace association
    Category  string            // category label
    Labels    map[string]string // arbitrary key-value labels
}
```

The `Emulator` struct holds a generic `Config` map, allowing it to represent any emulator type's settings without a rigid schema per emulator.

### YAML Format

```yaml
apiVersion: devopsmaestro.io/v1
kind: TerminalEmulator
metadata:
  name: my-emulator
  description: My custom emulator config
  category: developer
  labels:
    env: work
spec:
  type: wezterm
  themeRef: catppuccin-mocha
  config:
    font_size: 14
    opacity: 0.95
```

## Constructor

### NewEmulator

```go
func NewEmulator(name string, emulatorType EmulatorType) *Emulator
```

Creates a new `Emulator` with the given name and type. The `Config` map is initialized empty.

```go
e := emulator.NewEmulator("my-wezterm", emulator.EmulatorTypeWezterm)
e.Config["font_size"] = 14
e.ThemeRef = "catppuccin-mocha"
```

## Parser

Parse emulator resources from YAML data or files.

```go
func ParseYAMLFile(path string) (*Emulator, error)
func ParseYAML(data []byte) (*Emulator, error)
```

```go
e, err := emulator.ParseYAMLFile("/path/to/emulator.yaml")
if err != nil {
    log.Fatal(err)
}
```

## Store Interface

### EmulatorStore

```go
type EmulatorStore interface {
    Create(e *Emulator) error
    Get(name string) (*Emulator, error)
    Update(e *Emulator) error
    Upsert(e *Emulator) error
    Delete(name string) error
    List() ([]*Emulator, error)
    ListByType(emulatorType EmulatorType) ([]*Emulator, error)
    ListByWorkspace(workspace string) ([]*Emulator, error)
    Exists(name string) (bool, error)
    Close() error
}
```

## Errors

```go
var ErrNotFound = errors.New("emulator not found")
var ErrAlreadyExists = errors.New("emulator already exists")
```

Check for these sentinel errors when calling store methods:

```go
e, err := store.Get("my-emulator")
if errors.Is(err, emulator.ErrNotFound) {
    // handle not found
}
```

## Embedded Library

**Import:** `github.com/rmkohlman/MaestroTerminal/terminalops/emulator/library`

The `library` sub-package provides an in-memory library of curated emulator configurations loaded from embedded YAML files.

### EmulatorLibrary

```go
type EmulatorLibrary struct {
    // unexported fields
}
```

Implements the `Library` interface:

```go
type Library interface {
    Get(name string) (*emulator.Emulator, error)
    List() ([]*emulator.Emulator, error)
    ListByType(emulatorType emulator.EmulatorType) ([]*emulator.Emulator, error)
    ListByCategory(category string) ([]*emulator.Emulator, error)
    Exists(name string) bool
    Count() int
}
```

### Constructor Functions

```go
func NewEmulatorLibrary() *EmulatorLibrary
func NewLibraryFromDir(dir string) (*EmulatorLibrary, error)
func Default() *EmulatorLibrary
```

- `NewEmulatorLibrary()` — loads the embedded library
- `NewLibraryFromDir(dir string)` — loads YAML files from a directory on disk
- `Default()` — returns the global singleton embedded library

### Embedded Presets

| Name | Description |
|------|-------------|
| `alacritty-minimal` | Minimal Alacritty configuration |
| `developer` | Developer-focused configuration |
| `iterm2-macos` | iTerm2 macOS configuration |
| `kitty-poweruser` | Kitty power-user configuration |
| `maestro` | DevOpsMaestro default emulator config |
| `minimal` | Minimal cross-emulator configuration |

### Usage

```go
import "github.com/rmkohlman/MaestroTerminal/terminalops/emulator/library"

lib := library.Default()

// Get by name
e, err := lib.Get("developer")

// List all
all, err := lib.List()

// Filter by type
weztermConfigs, err := lib.ListByType(emulator.EmulatorTypeWezterm)

fmt.Printf("Library contains %d emulator configs\n", lib.Count())
```
