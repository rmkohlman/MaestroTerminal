# Packages

**Import:** `github.com/rmkohlman/MaestroTerminal/terminalops/package`

The `package` package (Go package name: `pkg`) provides types, a parser, and an embedded library for terminal package resources. A terminal package is a bundle of prompt, plugin, emulator, and shell settings that can be applied together.

## Types

### Package

```go
type Package struct {
    Extends          string       // name of another package to inherit from (single inheritance)
    Plugins          []string     // plugin names to include
    Prompts          []string     // prompt names to include
    Profiles         []string     // profile names to include
    PromptStyle      string       // name of the prompt style to apply
    PromptExtensions []string     // prompt extension names to apply
    Theme            string       // theme name
    WezTerm          *WezTermConfig // WezTerm-specific overrides
}
```

### WezTermConfig

```go
type WezTermConfig struct {
    FontSize    int    // font size in points
    ColorScheme string // color scheme name
    FontFamily  string // font family name
}
```

### StringOrSlice

```go
type StringOrSlice []string
```

A type that unmarshals from YAML as either a single string or a list of strings. Used internally for fields that accept both `"value"` and `["value1", "value2"]` syntax.

```go
func (s *StringOrSlice) UnmarshalYAML(value *yaml.Node) error
func (s StringOrSlice) MarshalYAML() (interface{}, error)
```

### Helper Methods

```go
func (p *Package) UsesModularPrompt() bool
```

Returns `true` if both `PromptStyle` is non-empty and `PromptExtensions` contains at least one entry. Use this to determine whether to run the prompt composer pipeline.

```go
if pkg.UsesModularPrompt() {
    // run composer pipeline
}
```

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
    - starship-default
  promptStyle: powerline
  promptExtensions:
    - git
    - directory
    - time
  theme: catppuccin-mocha
  wezterm:
    fontSize: 14
    colorScheme: Catppuccin Mocha
    fontFamily: MesloLGS Nerd Font Mono
```

### Single-string shorthand

Fields that use `StringOrSlice` accept either format:

```yaml
# single string
plugins: zsh-autosuggestions

# list
plugins:
  - zsh-autosuggestions
  - fzf
```

## Parser

### ParseYAMLFile

```go
func ParseYAMLFile(path string) (*Package, error)
```

Parses a single package from a YAML file.

### ParseYAML

```go
func ParseYAML(data []byte) (*Package, error)
```

Parses a single package from YAML bytes.

### ParseYAMLMultiple

```go
func ParseYAMLMultiple(data []byte) ([]*Package, error)
```

Parses multiple packages from a single YAML document using `---` as the separator between documents.

```go
data, _ := os.ReadFile("packages.yaml")
packages, err := pkg.ParseYAMLMultiple(data)
```

## Embedded Library

**Import:** `github.com/rmkohlman/MaestroTerminal/terminalops/package/library`

### Library

```go
type Library struct {
    // unexported fields
}
```

Provides an in-memory library of curated terminal packages loaded from embedded YAML files.

**Methods:**

```go
func (l *Library) Get(name string) (*pkg.Package, error)
func (l *Library) List() []*pkg.Package
func (l *Library) ListByCategory(category string) []*pkg.Package
func (l *Library) ListByTag(tag string) []*pkg.Package
func (l *Library) Categories() []string
func (l *Library) Tags() []string
func (l *Library) Count() int
func (l *Library) Has(name string) bool
func (l *Library) Info() []PackageInfo
```

### PackageInfo

```go
type PackageInfo struct {
    Name         string
    Description  string
    Category     string
    Tags         []string
    Extends      string
    PluginCount  int
    PromptCount  int
    ProfileCount int
}
```

Use `Info()` to get a lightweight summary of all packages without fully loading their configs.

### Embedded Packages

| Name | Description |
|------|-------------|
| `core` | Core base package |
| `developer` | Developer-focused package |
| `maestro` | DevOpsMaestro default package |

### Usage

```go
import pkglib "github.com/rmkohlman/MaestroTerminal/terminalops/package/library"

lib := pkglib.NewLibrary()

p, err := lib.Get("developer")
if err != nil {
    log.Fatal(err)
}

fmt.Println(p.UsesModularPrompt()) // true or false

for _, info := range lib.Info() {
    fmt.Printf("%s: %d plugins, %d prompts\n",
        info.Name, info.PluginCount, info.PromptCount)
}
```
