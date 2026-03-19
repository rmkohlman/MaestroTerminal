# Prompt Extensions

**Import:** `github.com/rmkohlman/MaestroTerminal/terminalops/prompt/extension`

The `extension` package defines `PromptExtension` — a content module that provides Starship module configurations for a named segment in a [PromptStyle](prompt-styles.md). Extensions are the content layer; styles are the layout layer.

## Types

### PromptExtension

```go
type PromptExtension struct {
    Segment  string                       // name of the segment this extension targets
    Provides []string                     // module names this extension contributes
    Modules  map[string]ExtensionModule   // module configurations keyed by module name
}
```

### ExtensionModule

```go
type ExtensionModule struct {
    Disabled bool           // disable this module
    Symbol   string         // symbol override
    Format   string         // Starship format string for this module
    Style    string         // style string (supports ${theme.X} variables)
    Options  map[string]any // additional Starship module options
}
```

**Style fields** must use palette key references via `${theme.X}` syntax. Hex color values are rejected by `Validate()`.

## Methods

### Validate

```go
func (pe *PromptExtension) Validate() error
```

Validates the extension definition. Returns an error if:

- The extension has no `Name` (from the resource metadata)
- `Segment` is not set
- No modules are defined in `Modules`
- Any `Style` field contains a hex color value instead of a palette variable

```go
if err := ext.Validate(); err != nil {
    log.Fatalf("invalid extension: %v", err)
}
```

## YAML Format

```yaml
apiVersion: devopsmaestro.io/v1
kind: PromptExtension
metadata:
  name: git
  description: Git branch and status modules
  category: vcs
spec:
  segment: vcs
  provides:
    - git_branch
    - git_status
  modules:
    git_branch:
      symbol: " "
      format: "[$symbol$branch(:$remote_branch)]($style) "
      style: "${theme.accent}"
    git_status:
      format: "([$all_status$ahead_behind]($style)) "
      style: "${theme.secondary}"
      options:
        ahead: ""
        behind: ""
        diverged: ""
        untracked: "?"
        modified: "!"
        staged: "+"
```

## Embedded Library

The `extension` package includes an embedded library of curated prompt extensions loaded from YAML files in the `extensions/` subdirectory.

### NewLibrary

```go
func NewLibrary() *Library
```

Creates a new library loaded from embedded extension YAML files.

**Library methods:**

```go
func (l *Library) Get(name string) (*PromptExtension, error)
func (l *Library) List() []*PromptExtension
func (l *Library) ListBySegment(segment string) []*PromptExtension
func (l *Library) ListByCategory(category string) []*PromptExtension
func (l *Library) Exists(name string) bool
func (l *Library) Count() int
```

### Embedded Extensions

The library ships with 9 built-in extensions (loaded from the embedded `extensions/` directory).

### Usage

```go
import "github.com/rmkohlman/MaestroTerminal/terminalops/prompt/extension"

lib := extension.NewLibrary()

// Get a specific extension
gitExt, err := lib.Get("git")
if err != nil {
    log.Fatal(err)
}

// List all extensions targeting the "vcs" segment
vcsExts := lib.ListBySegment("vcs")

fmt.Printf("Library contains %d extensions\n", lib.Count())
```

## Relationship to Styles and Composer

A `PromptExtension` targets a specific segment by name (via `Segment`). The `Provides` list declares which Starship module names appear in that segment. The [Prompt Composer](prompt-composer.md) matches extensions to segments in a style and assembles the final format string and module configuration.

```
PromptStyle (segments with names)
    +
PromptExtension (targets segment by name, provides modules)
    ↓
StarshipComposer
    ↓
ComposedPrompt (format string + module configs)
```

If a style defines a segment that has no matching extension, the composer skips that segment without error. Transitions are adjusted accordingly so powerline arrows remain visually consistent.
