# Shell

**Import:** `github.com/rmkohlman/MaestroTerminal/terminalops/shell`

The `shell` package provides types, a generator, a parser, and helper functions for shell configuration resources. It supports generating zsh, bash, and fish configuration files from a declarative `Shell` struct.

## Types

### ShellType

```go
type ShellType string
```

**Constants:**

| Constant | Value |
|----------|-------|
| `ShellTypeZsh` | `"zsh"` |
| `ShellTypeBash` | `"bash"` |
| `ShellTypeFish` | `"fish"` |

### Shell

```go
type Shell struct {
    Env         []EnvVar      // environment variable definitions
    Aliases     []Alias       // shell aliases
    Functions   []Function    // shell functions
    PathPrepend []string      // paths to prepend to $PATH
    PathAppend  []string      // paths to append to $PATH
    Options     []string      // shell options (e.g., zsh setopt values)
    History     *HistoryConfig // history configuration
    Keybindings []Keybinding  // key binding definitions
    RawConfig   string        // raw shell config appended verbatim
}
```

### EnvVar

```go
type EnvVar struct {
    Name        string // variable name
    Value       string // variable value
    Description string // documentation comment
    Expand      bool   // if true, expand $VAR references in Value
}
```

### Alias

```go
type Alias struct {
    Name        string // alias name
    Command     string // command the alias expands to
    Description string // documentation comment
    Global      bool   // if true, define as a zsh global alias (alias -g)
}
```

### Function

```go
type Function struct {
    Name        string // function name
    Body        string // function body (multi-line supported)
    Description string // documentation comment
}
```

### HistoryConfig

```go
type HistoryConfig struct {
    Size          int    // maximum number of history entries
    File          string // path to history file
    IgnoreDups    bool   // ignore duplicate commands
    IgnoreSpace   bool   // ignore commands starting with space
    ShareHistory  bool   // share history between sessions
    ExtendedFormat bool  // save timestamps with history entries
}
```

### Keybinding

```go
type Keybinding struct {
    Key         string // key sequence (e.g., "^R", "^[[A")
    Widget      string // zsh widget to bind to
    Command     string // command to execute (alternative to Widget)
    Description string // documentation comment
}
```

## YAML Format

```yaml
apiVersion: devopsmaestro.io/v1
kind: TerminalShell
metadata:
  name: zsh-default
  description: Default zsh configuration
  category: general
spec:
  type: zsh
  options:
    - AUTO_CD
    - CORRECT
    - HIST_VERIFY
  history:
    size: 10000
    file: ~/.zsh_history
    ignoreDups: true
    ignoreSpace: true
    shareHistory: true
    extendedFormat: true
  env:
    - name: EDITOR
      value: nvim
      description: Default text editor
    - name: GOPATH
      value: ~/go
      expand: true
  aliases:
    - name: ll
      command: ls -la
      description: Long listing
    - name: g
      command: git
  functions:
    - name: mkcd
      description: Make directory and cd into it
      body: |
        mkdir -p "$1" && cd "$1"
  keybindings:
    - key: "^R"
      widget: history-incremental-search-backward
      description: Reverse history search
  pathPrepend:
    - ~/bin
    - ~/.local/bin
  rawConfig: |
    # Source additional scripts
    [ -f ~/.dvt/.zshrc.dvt ] && source ~/.dvt/.zshrc.dvt
```

## Generator

### Generator

```go
type Generator struct {
    // unexported fields
}
```

Generates shell configuration files from a `Shell`. Dispatches to zsh, bash, or fish output based on the shell type.

### NewGenerator

```go
func NewGenerator() *Generator
```

### Methods

```go
func (g *Generator) Generate(s *Shell) (string, error)
func (g *Generator) WriteToFile(s *Shell, outputPath string) error
```

The generator produces output with a consistent structure:
1. Environment variable exports
2. PATH modifications
3. Shell options
4. History configuration
5. Aliases
6. Functions
7. Key bindings
8. Raw config (verbatim)

**Output filenames** (when using `WriteToFile`):

| Shell | Output File |
|-------|-------------|
| zsh | `.zshrc.workspace` |
| bash | `.bashrc.workspace` |
| fish | `config.fish.workspace` |

```go
gen := shell.NewGenerator()

s := &shell.Shell{
    Aliases: []shell.Alias{
        {Name: "ll", Command: "ls -la"},
    },
}

zsh, err := gen.Generate(s)
if err != nil {
    log.Fatal(err)
}
fmt.Println(zsh)
```

## Helper Functions

These functions provide sensible defaults for common shell configurations.

### DefaultZshOptions

```go
func DefaultZshOptions() []string
```

Returns a list of recommended zsh `setopt` values.

### DefaultHistoryConfig

```go
func DefaultHistoryConfig() *HistoryConfig
```

Returns a `HistoryConfig` with common defaults (large size, dedup enabled, shared history).

### CommonAliases

```go
func CommonAliases() []Alias
```

Returns a set of widely-used aliases (e.g., `ll`, `la`, `..`, `...`).

### DevAliases

```go
func DevAliases() []Alias
```

Returns aliases commonly used in developer workflows (e.g., git shortcuts, docker shortcuts).

### GetDefaults

```go
func GetDefaults() map[string]interface{}
```

Returns default shell metadata values:

```go
map[string]interface{}{
    "type":      "zsh",
    "framework": "oh-my-zsh",
    "theme":     "starship",
}
```

## Store Interface

### ShellStore

```go
type ShellStore interface {
    Create(s *ShellYAML) error
    Update(s *ShellYAML) error
    Upsert(s *ShellYAML) error
    Delete(name string) error
    Get(name string) (*ShellYAML, error)
    List() ([]*ShellYAML, error)
    ListByType(shellType string) ([]*ShellYAML, error)
    ListByCategory(category string) ([]*ShellYAML, error)
    Exists(name string) (bool, error)
    Close() error
}
```

## Parser

```go
func ParseFile(path string) (*ShellYAML, error)
func Parse(data []byte) (*ShellYAML, error)
```
