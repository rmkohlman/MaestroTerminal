# Prompt Styles

**Import:** `github.com/rmkohlman/MaestroTerminal/terminalops/prompt/style`

The `style` package defines `PromptStyle` — a layout definition for modular powerline-style prompts. A style specifies the segment structure (order, transitions, colors) but not the content. Content is provided by [Prompt Extensions](prompt-extensions.md) and assembled by the [Prompt Composer](prompt-composer.md).

## Types

### PromptStyle

```go
type PromptStyle struct {
    Segments []Segment // ordered list of prompt segments
    Suffix   string    // text appended after the last segment
}
```

### Segment

```go
type Segment struct {
    Name            string   // unique segment identifier
    Position        int      // sort order (lower = leftmost)
    StartTransition string   // powerline transition character before this segment
    StartColor      string   // background color at segment start (palette key)
    EndColor        string   // background color at segment end (palette key)
    Modules         []string // module names from extensions that appear in this segment
}
```

**Color fields** must reference palette keys (e.g., `"primary"`, `"surface0"`), not hex values. Hex values are rejected by `Validate()`.

## Methods

### GetSegments

```go
func (ps *PromptStyle) GetSegments() []Segment
```

Returns the segments sorted ascending by `Position`. Use this instead of accessing `Segments` directly to ensure correct ordering.

```go
for _, seg := range style.GetSegments() {
    fmt.Printf("Segment %d: %s\n", seg.Position, seg.Name)
}
```

### Validate

```go
func (ps *PromptStyle) Validate() error
```

Validates the style definition. Returns an error if:

- The style has no `Name` (from the resource metadata)
- No segments are defined
- Two or more segments share the same `Position`
- A color field contains a hex value (e.g., `#ff0000`) instead of a palette key

```go
if err := style.Validate(); err != nil {
    log.Fatalf("invalid style: %v", err)
}
```

## YAML Format

```yaml
apiVersion: devopsmaestro.io/v1
kind: PromptStyle
metadata:
  name: powerline
  description: Classic powerline segmented prompt
  category: powerline
spec:
  suffix: " "
  segments:
    - name: env
      position: 1
      startColor: surface1
      endColor: surface2
      modules:
        - os
        - python
    - name: path
      position: 2
      startTransition: ""
      startColor: surface2
      endColor: primary
      modules:
        - directory
    - name: vcs
      position: 3
      startColor: primary
      endColor: accent
      modules:
        - git_branch
        - git_status
    - name: status
      position: 4
      startColor: accent
      endColor: ""
      modules:
        - character
```

## Embedded Library

The `style` package includes an embedded library of curated prompt styles loaded from YAML files in the `styles/` subdirectory.

### NewLibrary

```go
func NewLibrary() *Library
```

Creates a new library loaded from embedded style YAML files.

**Library methods:**

```go
func (l *Library) Get(name string) (*PromptStyle, error)
func (l *Library) List() []*PromptStyle
func (l *Library) ListByCategory(category string) []*PromptStyle
func (l *Library) Exists(name string) bool
func (l *Library) Count() int
```

### Usage

```go
import "github.com/rmkohlman/MaestroTerminal/terminalops/prompt/style"

lib := style.NewLibrary()

s, err := lib.Get("powerline")
if err != nil {
    log.Fatal(err)
}

for _, seg := range s.GetSegments() {
    fmt.Printf("%d: %s (colors: %s -> %s)\n",
        seg.Position, seg.Name, seg.StartColor, seg.EndColor)
}
```

## Relationship to Extensions and Composer

A `PromptStyle` defines **where** modules appear (segment layout, positions, colors, transitions). [Prompt Extensions](prompt-extensions.md) define **what** content each module provides. The [Prompt Composer](prompt-composer.md) combines them into a complete `ComposedPrompt`.

```
PromptStyle  +  []PromptExtension  →  StarshipComposer  →  ComposedPrompt
(layout)         (content)                                  (format + modules)
```
