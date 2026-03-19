# Prompt Composer

**Import:** `github.com/rmkohlman/MaestroTerminal/terminalops/prompt/composer`

The `composer` package assembles a complete Starship prompt configuration from a [PromptStyle](prompt-styles.md) and a set of [PromptExtensions](prompt-extensions.md). It produces a `ComposedPrompt` that can be rendered by the `StarshipRenderer`.

## Types

### ComposedPrompt

```go
type ComposedPrompt struct {
    Format  string                   // complete Starship $format string
    Modules map[string]ModuleConfig  // module configurations keyed by module name
    Palette string                   // palette reference for theme resolution
}
```

### ModuleConfig

```go
type ModuleConfig struct {
    Disabled bool           // disable this module
    Symbol   string         // symbol override
    Format   string         // Starship format string for this module
    Style    string         // style (supports ${theme.X} variables)
    Options  map[string]any // additional Starship module options
}
```

## Interfaces

### PromptComposer

```go
type PromptComposer interface {
    Compose(
        promptStyle *style.PromptStyle,
        extensions []*extension.PromptExtension,
    ) (*ComposedPrompt, error)
}
```

The single method `Compose` takes a style (layout) and a slice of extensions (content) and returns an assembled `ComposedPrompt`.

## StarshipComposer

```go
type StarshipComposer struct {
    // unexported fields
}
```

The concrete implementation of `PromptComposer` for Starship. It:

1. Validates the style and all extensions
2. Matches each extension to its target segment by name
3. Builds the Starship `$format` string using `FormatBuilder`
4. Collects all module configurations from matched extensions
5. Skips segments with no matching extension (powerline transitions adjust automatically)

### NewStarshipComposer

```go
func NewStarshipComposer() *StarshipComposer
```

### Compose

```go
func (c *StarshipComposer) Compose(
    promptStyle *style.PromptStyle,
    extensions []*extension.PromptExtension,
) (*ComposedPrompt, error)
```

```go
import (
    "github.com/rmkohlman/MaestroTerminal/terminalops/prompt/composer"
    "github.com/rmkohlman/MaestroTerminal/terminalops/prompt/style"
    "github.com/rmkohlman/MaestroTerminal/terminalops/prompt/extension"
)

styleLib := style.NewLibrary()
extLib := extension.NewLibrary()

promptStyle, _ := styleLib.Get("powerline")
gitExt, _ := extLib.Get("git")
dirExt, _ := extLib.Get("directory")

c := composer.NewStarshipComposer()
composed, err := c.Compose(promptStyle, []*extension.PromptExtension{gitExt, dirExt})
if err != nil {
    log.Fatal(err)
}

fmt.Println(composed.Format)
```

## FormatBuilder

```go
type FormatBuilder struct {
    tracker *TransitionTracker
}
```

`FormatBuilder` constructs the Starship `$format` string segment by segment, tracking powerline arrow transitions between segments.

### NewFormatBuilder

```go
func NewFormatBuilder() *FormatBuilder
```

### Methods

```go
func (fb *FormatBuilder) Build(
    promptStyle *style.PromptStyle,
    extensions []*extension.PromptExtension,
) string

func (fb *FormatBuilder) AddSegment(
    segment style.Segment,
    moduleRefs []string,
)

func (fb *FormatBuilder) SkipSegment(segment style.Segment)

func (fb *FormatBuilder) BuildString(suffix string) string
```

| Method | Description |
|--------|-------------|
| `Build` | Convenience method: builds the complete format string from style and extensions |
| `AddSegment` | Adds a segment with the given module references to the format string |
| `SkipSegment` | Marks a segment as skipped — does NOT update the transition tracker's last end color |
| `BuildString` | Returns the accumulated format string, appending the given suffix |

## TransitionTracker

```go
type TransitionTracker struct {
    lastEndColor string
}
```

Tracks the last segment's end color to generate correct powerline arrow transitions between adjacent segments.

### Constants

```go
const PowerlineArrow = "\ue0b0" // U+E0B0: powerline right-pointing filled arrow
```

### Methods

```go
func (t *TransitionTracker) BuildTransition(newStartColor string) string
func (t *TransitionTracker) Skip(segment interface{})
func (t *TransitionTracker) SetLastEndColor(color string)
func (t *TransitionTracker) GetLastEndColor() string
```

| Method | Description |
|--------|-------------|
| `BuildTransition` | Returns a Starship-formatted transition string using `PowerlineArrow`, styled with the previous end color as foreground and new start color as background |
| `Skip` | Marks a segment skipped. Does NOT update `lastEndColor`, so the next real segment transitions correctly from the last non-skipped segment |
| `SetLastEndColor` | Manually set the last end color |
| `GetLastEndColor` | Get the current last end color |

## Composing with the Renderer

After composing, pass the `ComposedPrompt` to `prompt.StarshipRenderer.RenderComposed` to resolve theme variables and produce TOML:

```go
import "github.com/rmkohlman/MaestroTerminal/terminalops/prompt"

renderer := &prompt.StarshipRenderer{IndentSize: 2}
toml, err := renderer.RenderComposed(composed, "my-prompt", myPalette)
if err != nil {
    log.Fatal(err)
}
```

## Full Pipeline Example

```go
package main

import (
    "fmt"
    "log"

    "github.com/rmkohlman/MaestroTerminal/terminalops/prompt"
    "github.com/rmkohlman/MaestroTerminal/terminalops/prompt/composer"
    "github.com/rmkohlman/MaestroTerminal/terminalops/prompt/extension"
    "github.com/rmkohlman/MaestroTerminal/terminalops/prompt/style"
)

func main() {
    // Load a style and extensions from embedded libraries
    styleLib := style.NewLibrary()
    extLib := extension.NewLibrary()

    promptStyle, err := styleLib.Get("powerline")
    if err != nil {
        log.Fatal(err)
    }

    gitExt, _ := extLib.Get("git")
    dirExt, _ := extLib.Get("directory")

    // Compose
    c := composer.NewStarshipComposer()
    composed, err := c.Compose(promptStyle, []*extension.PromptExtension{gitExt, dirExt})
    if err != nil {
        log.Fatal(err)
    }

    // Render to TOML (with a palette for theme resolution)
    renderer := &prompt.StarshipRenderer{IndentSize: 2}
    toml, err := renderer.RenderComposed(composed, "my-prompt", nil)
    if err != nil {
        log.Fatal(err)
    }

    fmt.Println(toml)
}
```
