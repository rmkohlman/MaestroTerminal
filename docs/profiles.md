# Profiles

**Import:** `github.com/rmkohlman/MaestroTerminal/terminalops/profile`

The `profile` package provides types, a generator, a parser, and a store interface for terminal profile resources. A profile aggregates a prompt, a set of plugins, and a shell config into a single resource that can be generated into ready-to-use config files.

## Types

### Profile

```go
type Profile struct {
    // Inline configurations (fully defined within the profile)
    Prompt  *prompt.Prompt    // inline Starship prompt config
    Plugins []*plugin.Plugin  // inline plugin definitions
    Shell   *shell.Shell      // inline shell config

    // References to named resources in stores
    PromptRef  string   // name of a stored PromptYAML resource
    PluginRefs []string // names of stored Plugin resources
    ShellRef   string   // name of a stored ShellYAML resource

    // Theme reference
    ThemeRef string // name of a theme resource
}
```

A `Profile` can use either inline config or references to named resources — or mix both. Use `HasInlineConfig()` and `HasReferences()` to check which approach is in use.

### Helper Methods

```go
func (p *Profile) HasInlineConfig() bool
func (p *Profile) HasReferences() bool
```

- `HasInlineConfig()` returns `true` if any of `Prompt`, `Plugins`, or `Shell` are set inline
- `HasReferences()` returns `true` if any of `PromptRef`, `PluginRefs`, or `ShellRef` are set

### GeneratedConfig

```go
type GeneratedConfig struct {
    StarshipTOML  string // Starship prompt TOML configuration
    ZshrcPlugins  string // zsh plugin loading snippet
    ZshrcShell    string // zsh shell config snippet (aliases, env, functions, etc.)
    ZshrcFull     string // complete .zshrc combining plugins and shell config
    InstallScript string // bash install script for plugins requiring external setup
}
```

## YAML Format

```yaml
apiVersion: devopsmaestro.io/v1
kind: TerminalProfile
metadata:
  name: default
  description: Default DevOpsMaestro profile
  category: general
spec:
  # Option 1: Use references to stored resources
  promptRef: starship-default
  pluginRefs:
    - zsh-autosuggestions
    - zsh-syntax-highlighting
    - fzf
  shellRef: zsh-default
  themeRef: catppuccin-mocha

  # Option 2: Inline config (prompt, plugins, shell defined directly)
  # prompt:
  #   addNewline: true
  #   format: "$directory$character"
```

## Generator

### Generator

```go
type Generator struct {
    // unexported fields
}
```

Generates `GeneratedConfig` from a `Profile`. Internally uses `StarshipGenerator`, `ZshGenerator`, and `shell.Generator`.

### NewGenerator

```go
func NewGenerator(pluginDir string) *Generator
```

Creates a new `Generator`. `pluginDir` is passed to the underlying `ZshGenerator` for manual plugin installs. Pass an empty string to use the default plugin directory.

### Methods

```go
func (g *Generator) Generate(p *Profile) (*GeneratedConfig, error)
func (g *Generator) GenerateZshrc(p *Profile) (string, error)
```

| Method | Description |
|--------|-------------|
| `Generate` | Generates all config files: `StarshipTOML`, `ZshrcPlugins`, `ZshrcShell`, `ZshrcFull`, and `InstallScript` |
| `GenerateZshrc` | Generates only the combined `.zshrc` content |

```go
gen := profile.NewGenerator("")

p := profile.DefaultProfile()
cfg, err := gen.Generate(p)
if err != nil {
    log.Fatal(err)
}

fmt.Println(cfg.StarshipTOML)
os.WriteFile("starship.toml", []byte(cfg.StarshipTOML), 0644)
os.WriteFile(".zshrc.dvt", []byte(cfg.ZshrcFull), 0644)
```

## Preset Functions

Three preset profiles are available as Go functions — no file loading required.

```go
func DefaultProfile() *Profile
func MinimalProfile() *Profile
func PowerUserProfile() *Profile
```

| Function | Description |
|----------|-------------|
| `DefaultProfile()` | Balanced setup: Starship prompt, essential plugins, standard zsh config |
| `MinimalProfile()` | Minimal setup for lightweight or constrained environments |
| `PowerUserProfile()` | Feature-rich setup with extended plugins and prompt customization |

```go
p := profile.DefaultProfile()
gen := profile.NewGenerator("")
cfg, err := gen.Generate(p)
```

## Store Interface

### ProfileStore

```go
type ProfileStore interface {
    Create(p *Profile) error
    Update(p *Profile) error
    Upsert(p *Profile) error
    Delete(name string) error
    Get(name string) (*Profile, error)
    List() ([]*Profile, error)
    ListByCategory(category string) ([]*Profile, error)
    Exists(name string) (bool, error)
    SetActive(name string) error
    GetActive() (*Profile, error)
    Close() error
}
```

`SetActive` and `GetActive` track the currently active profile. The active profile name is persisted in a `.active-profile` file in the profiles directory.

## Parser

```go
func ParseFile(path string) (*Profile, error)
func Parse(data []byte) (*Profile, error)
```

```go
p, err := profile.ParseFile("/path/to/profile.yaml")
```
