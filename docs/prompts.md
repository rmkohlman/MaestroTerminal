# Prompts

**Import:** `github.com/rmkohlman/MaestroTerminal/terminalops/prompt`

The `prompt` package provides types, a Starship config generator, a renderer, a parser, and a store interface for terminal prompt resources. Currently supports [Starship](https://starship.rs/) prompt configurations.

## Types

### PromptType

```go
type PromptType string
```

**Constants:**

| Constant | Value |
|----------|-------|
| `PromptTypeStarship` | `"starship"` |
| `PromptTypePowerlevel10k` | `"powerlevel10k"` |
| `PromptTypeOhMyPosh` | `"oh-my-posh"` |

### Prompt

```go
type Prompt struct {
    AddNewline  bool                    // add newline before prompt
    Palette     string                  // palette reference ("theme" for theme-aware)
    Format      string                  // Starship format string
    Modules     map[string]ModuleConfig // module-level configuration
    Character   *CharacterConfig        // character module config
    PaletteRef  string                  // explicit palette resource name
    Colors      map[string]string       // direct color overrides
    RawConfig   string                  // raw TOML to append verbatim
}
```

### ModuleConfig

```go
type ModuleConfig struct {
    Disabled bool              // disable this module entirely
    Format   string            // module format string
    Style    string            // module style (supports ${theme.X} variables)
    Symbol   string            // symbol override
    Options  map[string]any    // additional module-specific options
}
```

### CharacterConfig

```go
type CharacterConfig struct {
    SuccessSymbol string // symbol for successful command
    ErrorSymbol   string // symbol for failed command
    ViCmdSymbol   string // symbol in vi command mode
    ViInsSymbol   string // symbol in vi insert mode
}
```

### PromptYAML

`PromptYAML` is the YAML resource wrapper for a `Prompt`. Use `NewTerminalPrompt` to create one.

## Constructor

### NewTerminalPrompt

```go
func NewTerminalPrompt(name string) *PromptYAML
```

Creates a new `PromptYAML` with sensible defaults:

- `AddNewline = true`
- `Palette = "theme"`

```go
p := prompt.NewTerminalPrompt("my-prompt")
p.Prompt.Format = "$directory$git_branch$character"
```

## YAML Format

```yaml
apiVersion: devopsmaestro.io/v1
kind: TerminalPrompt
metadata:
  name: my-prompt
  description: My custom Starship prompt
  category: developer
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

## Theme Variable Resolution

Styles support `${theme.X}` variable syntax, which is resolved against a `MaestroPalette` at render time.

**Supported theme variables:**

| Variable | Maps to |
|----------|---------|
| `${theme.primary}` | Primary palette color |
| `${theme.secondary}` | Secondary palette color |
| `${theme.accent}` | Accent palette color |
| `${theme.bg}` / `${theme.fg}` | Background / foreground |
| `${theme.success}` / `${theme.error}` / `${theme.warning}` / `${theme.info}` | Diagnostic colors |
| `${theme.red}`, `${theme.green}`, `${theme.blue}`, etc. | Standard named colors |
| `${theme.bright_red}`, `${theme.bright_green}`, etc. | Bright color variants |
| `${theme.crust}`, `${theme.mantle}`, `${theme.base}` | Catppuccin-style surface colors |
| `${theme.surface0}`, `${theme.surface1}`, `${theme.surface2}` | Surface layers |
| `${theme.overlay0}`, `${theme.overlay1}`, `${theme.overlay2}` | Overlay layers |
| `${theme.text}`, `${theme.subtext0}`, `${theme.subtext1}` | Text colors |
| `${theme.lavender}`, `${theme.sky}`, `${theme.sapphire}`, `${theme.teal}` | Accent colors |
| `${theme.peach}`, `${theme.maroon}`, `${theme.pink}`, `${theme.mauve}` | Warm accent colors |
| `${theme.flamingo}`, `${theme.rosewater}` | Soft accent colors |

## Generator

### StarshipGenerator

```go
type StarshipGenerator struct {
    // unexported fields
}
```

Generates Starship TOML configuration from a `Prompt`.

### NewStarshipGenerator

```go
func NewStarshipGenerator() *StarshipGenerator
```

### Methods

```go
func (g *StarshipGenerator) Generate(p *Prompt) (string, error)
func (g *StarshipGenerator) WriteToFile(p *Prompt, outputPath string) error
func (g *StarshipGenerator) GenerateMinimal() string
func (g *StarshipGenerator) DefaultModules() map[string]ModuleConfig
func (g *StarshipGenerator) FormatWithColors(text, color string) string
func (g *StarshipGenerator) BuildFormat(modules ...string) string
```

| Method | Description |
|--------|-------------|
| `Generate` | Generates complete Starship TOML as a string |
| `WriteToFile` | Generates and writes to a file path |
| `GenerateMinimal` | Returns a minimal Starship TOML with no customization |
| `DefaultModules` | Returns the default module configurations |
| `FormatWithColors` | Wraps text in Starship color syntax |
| `BuildFormat` | Builds a Starship format string from module names |

```go
gen := prompt.NewStarshipGenerator()

toml, err := gen.Generate(p.Prompt)
if err != nil {
    log.Fatal(err)
}

// Or write directly to file
err = gen.WriteToFile(p.Prompt, "/home/user/.config/starship.toml")
```

## Renderer

### StarshipRenderer

```go
type StarshipRenderer struct {
    IndentSize int // TOML indentation size (default: 2)
}
```

The `StarshipRenderer` implements the `PromptRenderer` interface. It resolves `${theme.X}` variables against a `MaestroPalette` before generating TOML.

### PromptRenderer Interface

```go
type PromptRenderer interface {
    Render(prompt *PromptYAML, pal *palette.Palette) (string, error)
    RenderToFile(prompt *PromptYAML, pal *palette.Palette, outputPath string) error
    RenderComposed(composed *composer.ComposedPrompt, name string, pal *palette.Palette) (string, error)
    RenderComposedToFile(composed *composer.ComposedPrompt, name string, pal *palette.Palette, outputPath string) error
}
```

Use the renderer (rather than the generator) when you have a palette and want theme variables resolved:

```go
renderer := &prompt.StarshipRenderer{IndentSize: 2}
toml, err := renderer.Render(promptYAML, myPalette)
```

## Store Interface

### PromptStore

```go
type PromptStore interface {
    Create(p *PromptYAML) error
    Update(p *PromptYAML) error
    Upsert(p *PromptYAML) error
    Delete(name string) error
    Get(name string) (*PromptYAML, error)
    List() ([]*PromptYAML, error)
    ListByType(promptType string) ([]*PromptYAML, error)
    ListByCategory(category string) ([]*PromptYAML, error)
    Exists(name string) bool
    Close() error
}
```

Note: `Exists` returns a plain `bool` (no error).

## In-Memory Store

### MemoryStore

```go
type MemoryStore struct {
    // unexported fields (sync.RWMutex protected)
}
```

`MemoryStore` implements `PromptStore` using an in-memory map with read-write mutex protection. Use it for testing or ephemeral prompt management.

```go
store := prompt.NewMemoryStore()
store.Upsert(myPrompt)
p, _ := store.Get("my-prompt")
```

## Parser

```go
func ParseFile(path string) (*PromptYAML, error)
func Parse(data []byte) (*PromptYAML, error)
func ToYAMLBytes(p *PromptYAML) ([]byte, error)
func ToYAMLString(p *PromptYAML) (string, error)
```

```go
p, err := prompt.ParseFile("/path/to/prompt.yaml")

// Round-trip to YAML
yaml, err := prompt.ToYAMLString(p)
```
