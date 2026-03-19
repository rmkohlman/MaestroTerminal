# Manager

**Import:** `github.com/rmkohlman/MaestroTerminal/terminalops`

The root `terminalops` package provides the `Manager` — the top-level coordinator for all terminal configuration operations. It wires together file-backed stores and generators for prompts, plugins, shells, and profiles.

## Types

### Manager

```go
type Manager struct {
    // unexported fields
}
```

The `Manager` owns file-backed stores and generators for all resource types. It manages a config directory (default: `~/.dvt`) with subdirectories for each resource type.

**Config directory layout:**

```
~/.dvt/
  prompts/
  plugins/
  shells/
  profiles/
    .active-profile
```

### Options

```go
type Options struct {
    ConfigDir string
}
```

Use `Options` to override the default config directory when creating a `Manager` with `NewWithOptions`.

## Constructor Functions

### New

```go
func New() (*Manager, error)
```

Creates a `Manager` using the default config directory (`~/.dvt`). Creates all required subdirectories if they do not exist.

```go
mgr, err := terminalops.New()
if err != nil {
    log.Fatal(err)
}
```

### NewWithOptions

```go
func NewWithOptions(opts Options) (*Manager, error)
```

Creates a `Manager` with custom options. Use this to specify an alternate config directory.

```go
mgr, err := terminalops.NewWithOptions(terminalops.Options{
    ConfigDir: "/custom/path/.dvt",
})
```

## Store Accessors

The `Manager` exposes its stores through accessor methods. Each store is backed by the filesystem.

```go
func (m *Manager) PromptStore() PromptStore
func (m *Manager) PluginStore() PluginStore
func (m *Manager) ShellStore() ShellStore
func (m *Manager) ProfileStore() ProfileStore
```

See [Store Interfaces](#store-interfaces) below for the full interface for each store.

## Methods

### InstallPreset

```go
func (m *Manager) InstallPreset(name string) error
```

Populates the config directory with a built-in preset. Preset names: `"default"`, `"minimal"`, `"power-user"`.

```go
if err := mgr.InstallPreset("default"); err != nil {
    log.Fatal(err)
}
```

### GenerateProfile

```go
func (m *Manager) GenerateProfile(p *profile.Profile) (*profile.GeneratedConfig, error)
```

Generates configuration from a `Profile` and returns the result as a `GeneratedConfig` struct. Does not write any files.

```go
p, _ := mgr.ProfileStore().Get("default")
cfg, err := mgr.GenerateProfile(p)
if err != nil {
    log.Fatal(err)
}
fmt.Println(cfg.StarshipTOML)
fmt.Println(cfg.ZshrcFull)
```

### GenerateProfileToDir

```go
func (m *Manager) GenerateProfileToDir(p *profile.Profile, outputDir string) error
```

Generates configuration from a `Profile` and writes the output files to `outputDir`.

Output files:
- `starship.toml` — Starship prompt configuration
- `.zshrc.dvt` — Complete zsh configuration

```go
p, _ := mgr.ProfileStore().Get("default")
err := mgr.GenerateProfileToDir(p, "/home/user/.config/terminal")
```

## Store Interfaces

### PromptStore

```go
type PromptStore interface {
    Create(p *prompt.PromptYAML) error
    Update(p *prompt.PromptYAML) error
    Upsert(p *prompt.PromptYAML) error
    Delete(name string) error
    Get(name string) (*prompt.PromptYAML, error)
    List() ([]*prompt.PromptYAML, error)
    ListByType(promptType string) ([]*prompt.PromptYAML, error)
    ListByCategory(category string) ([]*prompt.PromptYAML, error)
    Exists(name string) bool
    Close() error
}
```

Note: `Exists` returns a plain `bool` (not `(bool, error)`), which is unique among the store interfaces.

### PluginStore

```go
type PluginStore interface {
    Create(p *plugin.Plugin) error
    Update(p *plugin.Plugin) error
    Upsert(p *plugin.Plugin) error
    Delete(name string) error
    Get(name string) (*plugin.Plugin, error)
    List() ([]*plugin.Plugin, error)
    ListByManager(manager string) ([]*plugin.Plugin, error)
    ListByCategory(category string) ([]*plugin.Plugin, error)
    Exists(name string) (bool, error)
    Close() error
}
```

### ShellStore

```go
type ShellStore interface {
    Create(s *shell.ShellYAML) error
    Update(s *shell.ShellYAML) error
    Upsert(s *shell.ShellYAML) error
    Delete(name string) error
    Get(name string) (*shell.ShellYAML, error)
    List() ([]*shell.ShellYAML, error)
    ListByType(shellType string) ([]*shell.ShellYAML, error)
    ListByCategory(category string) ([]*shell.ShellYAML, error)
    Exists(name string) (bool, error)
    Close() error
}
```

### ProfileStore

```go
type ProfileStore interface {
    Create(p *profile.Profile) error
    Update(p *profile.Profile) error
    Upsert(p *profile.Profile) error
    Delete(name string) error
    Get(name string) (*profile.Profile, error)
    List() ([]*profile.Profile, error)
    ListByCategory(category string) ([]*profile.Profile, error)
    Exists(name string) (bool, error)
    SetActive(name string) error
    GetActive() (*profile.Profile, error)
    Close() error
}
```

`ProfileStore` has two additional methods beyond the standard store interface:

- `SetActive(name string) error` — marks a profile as the active profile (stored in `.active-profile`)
- `GetActive() (*profile.Profile, error)` — retrieves the currently active profile

## Generator Interfaces

The manager internally uses these generator interfaces. You can use them directly if you bypass the Manager.

### PromptGenerator

```go
type PromptGenerator interface {
    Generate(p *prompt.Prompt) (string, error)
    WriteToFile(p *prompt.Prompt, outputPath string) error
}
```

### PluginGenerator

```go
type PluginGenerator interface {
    Generate(plugins []*plugin.Plugin) (string, error)
    GenerateInstallScript(plugins []*plugin.Plugin) string
}
```

### ShellGenerator

```go
type ShellGenerator interface {
    Generate(s *shell.Shell) (string, error)
    WriteToFile(s *shell.Shell, outputPath string) error
}
```

### ProfileGenerator

```go
type ProfileGenerator interface {
    Generate(p *profile.Profile) (*profile.GeneratedConfig, error)
    GenerateZshrc(p *profile.Profile) (string, error)
}
```

## Library Interfaces

These interfaces are defined in the root package for cross-package use.

### PromptLibrary

```go
type PromptLibrary interface {
    Get(name string) (*prompt.PromptYAML, error)
    List() ([]*prompt.PromptYAML, error)
    ListByCategory(category string) ([]*prompt.PromptYAML, error)
    Exists(name string) bool
}
```

### PluginLibrary

```go
type PluginLibrary interface {
    Get(name string) (*plugin.Plugin, error)
    List() ([]*plugin.Plugin, error)
    ListByCategory(category string) ([]*plugin.Plugin, error)
    Exists(name string) bool
}
```

## File Store Implementations

The `Manager` uses these concrete file-backed store implementations internally:

| Interface | Implementation |
|-----------|---------------|
| `PromptStore` | `FilePromptStore` |
| `PluginStore` | `FilePluginStore` |
| `ShellStore` | `FileShellStore` |
| `ProfileStore` | `FileProfileStore` |

These are not exported as part of the public API surface — access stores via the `Manager` accessor methods.
