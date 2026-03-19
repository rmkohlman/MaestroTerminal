# API Reference

Complete API reference for all packages in `github.com/rmkohlman/MaestroTerminal`.

---

## terminalops

**Import:** `github.com/rmkohlman/MaestroTerminal/terminalops`

### Types

```go
type Manager struct { /* unexported */ }

type Options struct {
    ConfigDir string
}
```

### Functions

```go
func New() (*Manager, error)
func NewWithOptions(opts Options) (*Manager, error)
```

### Manager Methods

```go
func (m *Manager) PromptStore() PromptStore
func (m *Manager) PluginStore() PluginStore
func (m *Manager) ShellStore() ShellStore
func (m *Manager) ProfileStore() ProfileStore
func (m *Manager) InstallPreset(name string) error
func (m *Manager) GenerateProfile(p *profile.Profile) (*profile.GeneratedConfig, error)
func (m *Manager) GenerateProfileToDir(p *profile.Profile, outputDir string) error
```

### Interfaces

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

type PromptGenerator interface {
    Generate(p *prompt.Prompt) (string, error)
    WriteToFile(p *prompt.Prompt, outputPath string) error
}

type PluginGenerator interface {
    Generate(plugins []*plugin.Plugin) (string, error)
    GenerateInstallScript(plugins []*plugin.Plugin) string
}

type ShellGenerator interface {
    Generate(s *shell.Shell) (string, error)
    WriteToFile(s *shell.Shell, outputPath string) error
}

type ProfileGenerator interface {
    Generate(p *profile.Profile) (*profile.GeneratedConfig, error)
    GenerateZshrc(p *profile.Profile) (string, error)
}

type PromptLibrary interface {
    Get(name string) (*prompt.PromptYAML, error)
    List() ([]*prompt.PromptYAML, error)
    ListByCategory(category string) ([]*prompt.PromptYAML, error)
    Exists(name string) bool
}

type PluginLibrary interface {
    Get(name string) (*plugin.Plugin, error)
    List() ([]*plugin.Plugin, error)
    ListByCategory(category string) ([]*plugin.Plugin, error)
    Exists(name string) bool
}
```

---

## terminalops/emulator

**Import:** `github.com/rmkohlman/MaestroTerminal/terminalops/emulator`

### Types

```go
type EmulatorType string

const (
    EmulatorTypeWezterm  EmulatorType = "wezterm"
    EmulatorTypeAlacritty EmulatorType = "alacritty"
    EmulatorTypeKitty    EmulatorType = "kitty"
    EmulatorTypeITerm2   EmulatorType = "iterm2"
)

type Emulator struct {
    Config    map[string]any
    ThemeRef  string
    Workspace string
    Category  string
    Labels    map[string]string
}
```

### Functions

```go
func NewEmulator(name string, emulatorType EmulatorType) *Emulator
func ParseYAMLFile(path string) (*Emulator, error)
func ParseYAML(data []byte) (*Emulator, error)
```

### Interfaces

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

### Errors

```go
var ErrNotFound = errors.New("emulator not found")
var ErrAlreadyExists = errors.New("emulator already exists")
```

### emulator/library

```go
type Library interface {
    Get(name string) (*emulator.Emulator, error)
    List() ([]*emulator.Emulator, error)
    ListByType(emulatorType emulator.EmulatorType) ([]*emulator.Emulator, error)
    ListByCategory(category string) ([]*emulator.Emulator, error)
    Exists(name string) bool
    Count() int
}

func NewEmulatorLibrary() *EmulatorLibrary
func NewLibraryFromDir(dir string) (*EmulatorLibrary, error)
func Default() *EmulatorLibrary
```

---

## terminalops/package

**Import:** `github.com/rmkohlman/MaestroTerminal/terminalops/package`

Go package name: `pkg`

### Types

```go
type Package struct {
    Extends          string
    Plugins          []string
    Prompts          []string
    Profiles         []string
    PromptStyle      string
    PromptExtensions []string
    Theme            string
    WezTerm          *WezTermConfig
}

type WezTermConfig struct {
    FontSize    int
    ColorScheme string
    FontFamily  string
}

type StringOrSlice []string
```

### Methods

```go
func (p *Package) UsesModularPrompt() bool
func (s *StringOrSlice) UnmarshalYAML(value *yaml.Node) error
func (s StringOrSlice) MarshalYAML() (interface{}, error)
```

### Functions

```go
func ParseYAMLFile(path string) (*Package, error)
func ParseYAML(data []byte) (*Package, error)
func ParseYAMLMultiple(data []byte) ([]*Package, error)
```

### package/library

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

---

## terminalops/plugin

**Import:** `github.com/rmkohlman/MaestroTerminal/terminalops/plugin`

### Types

```go
type PluginManager string

const (
    PluginManagerZinit    PluginManager = "zinit"
    PluginManagerOhMyZsh  PluginManager = "oh-my-zsh"
    PluginManagerAntigen  PluginManager = "antigen"
    PluginManagerManual   PluginManager = "manual"
    PluginManagerSheldon  PluginManager = "sheldon"
)

type LoadMode string

const (
    LoadModeImmediate LoadMode = "immediate"
    LoadModeDeferred  LoadMode = "deferred"
    LoadModeLazy      LoadMode = "lazy"
)

type Plugin struct {
    Repo          string
    Source        string
    Branch        string
    Tag           string
    Manager       PluginManager
    LoadMode      LoadMode
    OhMyZshPlugin string
    SourceFiles   []string
    Config        string
    Env           map[string]string
    Dependencies  []string
    Priority      int
}

type ZshGenerator struct {
    PluginDir string
}
```

### Functions

```go
func NewPlugin(name, repo string) *Plugin
func NewZshGenerator(pluginDir string) *ZshGenerator
func ParseYAMLFile(path string) (*Plugin, error)
func ParseYAML(data []byte) (*Plugin, error)
```

### Plugin Methods

```go
func (p *Plugin) IsOhMyZshBuiltin() bool
func (p *Plugin) GetSourceURL() string
```

### ZshGenerator Methods

```go
func (g *ZshGenerator) Generate(plugins []*Plugin) (string, error)
func (g *ZshGenerator) GenerateInstallScript(plugins []*Plugin) string
```

### Interfaces

```go
type PluginStore interface {
    Create(p *Plugin) error
    Update(p *Plugin) error
    Upsert(p *Plugin) error
    Delete(name string) error
    Get(name string) (*Plugin, error)
    List() ([]*Plugin, error)
    ListByManager(manager string) ([]*Plugin, error)
    ListByCategory(category string) ([]*Plugin, error)
    Exists(name string) (bool, error)
    Close() error
}
```

### plugin/library

```go
func (l *Library) Get(name string) (*plugin.Plugin, error)
func (l *Library) List() ([]*plugin.Plugin, error)
func (l *Library) ListByManager(manager plugin.PluginManager) ([]*plugin.Plugin, error)
func (l *Library) ListByCategory(category string) ([]*plugin.Plugin, error)
func (l *Library) Exists(name string) bool
func (l *Library) Count() int
func (l *Library) EssentialPlugins() []*plugin.Plugin
```

---

## terminalops/prompt

**Import:** `github.com/rmkohlman/MaestroTerminal/terminalops/prompt`

### Types

```go
type PromptType string

const (
    PromptTypeStarship     PromptType = "starship"
    PromptTypePowerlevel10k PromptType = "powerlevel10k"
    PromptTypeOhMyPosh     PromptType = "oh-my-posh"
)

const KindTerminalPrompt = "TerminalPrompt"

type Prompt struct {
    AddNewline  bool
    Palette     string
    Format      string
    Modules     map[string]ModuleConfig
    Character   *CharacterConfig
    PaletteRef  string
    Colors      map[string]string
    RawConfig   string
}

type ModuleConfig struct {
    Disabled bool
    Format   string
    Style    string
    Symbol   string
    Options  map[string]any
}

type CharacterConfig struct {
    SuccessSymbol string
    ErrorSymbol   string
    ViCmdSymbol   string
    ViInsSymbol   string
}

type StarshipGenerator struct { /* unexported */ }

type StarshipRenderer struct {
    IndentSize int
}

type MemoryStore struct { /* unexported */ }
```

### Functions

```go
func NewTerminalPrompt(name string) *PromptYAML
func NewStarshipGenerator() *StarshipGenerator
func NewMemoryStore() *MemoryStore
func ParseFile(path string) (*PromptYAML, error)
func Parse(data []byte) (*PromptYAML, error)
func ToYAMLBytes(p *PromptYAML) ([]byte, error)
func ToYAMLString(p *PromptYAML) (string, error)
```

### StarshipGenerator Methods

```go
func (g *StarshipGenerator) Generate(p *Prompt) (string, error)
func (g *StarshipGenerator) WriteToFile(p *Prompt, outputPath string) error
func (g *StarshipGenerator) GenerateMinimal() string
func (g *StarshipGenerator) DefaultModules() map[string]ModuleConfig
func (g *StarshipGenerator) FormatWithColors(text, color string) string
func (g *StarshipGenerator) BuildFormat(modules ...string) string
```

### Interfaces

```go
type PromptRenderer interface {
    Render(prompt *PromptYAML, pal *palette.Palette) (string, error)
    RenderToFile(prompt *PromptYAML, pal *palette.Palette, outputPath string) error
    RenderComposed(composed *composer.ComposedPrompt, name string, pal *palette.Palette) (string, error)
    RenderComposedToFile(composed *composer.ComposedPrompt, name string, pal *palette.Palette, outputPath string) error
}

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

---

## terminalops/prompt/style

**Import:** `github.com/rmkohlman/MaestroTerminal/terminalops/prompt/style`

### Types

```go
const KindPromptStyle = "PromptStyle"

type PromptStyle struct {
    Segments []Segment
    Suffix   string
}

type Segment struct {
    Name            string
    Position        int
    StartTransition string
    StartColor      string
    EndColor        string
    Modules         []string
}
```

### Methods

```go
func (ps *PromptStyle) GetSegments() []Segment
func (ps *PromptStyle) Validate() error
```

### Library

```go
func NewLibrary() *Library

func (l *Library) Get(name string) (*PromptStyle, error)
func (l *Library) List() []*PromptStyle
func (l *Library) ListByCategory(category string) []*PromptStyle
func (l *Library) Exists(name string) bool
func (l *Library) Count() int
```

---

## terminalops/prompt/extension

**Import:** `github.com/rmkohlman/MaestroTerminal/terminalops/prompt/extension`

### Types

```go
const KindPromptExtension = "PromptExtension"

type PromptExtension struct {
    Segment  string
    Provides []string
    Modules  map[string]ExtensionModule
}

type ExtensionModule struct {
    Disabled bool
    Symbol   string
    Format   string
    Style    string
    Options  map[string]any
}
```

### Methods

```go
func (pe *PromptExtension) Validate() error
```

### Library

```go
func NewLibrary() *Library

func (l *Library) Get(name string) (*PromptExtension, error)
func (l *Library) List() []*PromptExtension
func (l *Library) ListBySegment(segment string) []*PromptExtension
func (l *Library) ListByCategory(category string) []*PromptExtension
func (l *Library) Exists(name string) bool
func (l *Library) Count() int
```

---

## terminalops/prompt/composer

**Import:** `github.com/rmkohlman/MaestroTerminal/terminalops/prompt/composer`

### Types

```go
type ComposedPrompt struct {
    Format  string
    Modules map[string]ModuleConfig
    Palette string
}

type ModuleConfig struct {
    Disabled bool
    Symbol   string
    Format   string
    Style    string
    Options  map[string]any
}

type StarshipComposer struct { /* unexported */ }

type FormatBuilder struct { /* unexported */ }

type TransitionTracker struct { /* unexported */ }

const PowerlineArrow = "\ue0b0"
```

### Interfaces

```go
type PromptComposer interface {
    Compose(
        promptStyle *style.PromptStyle,
        extensions []*extension.PromptExtension,
    ) (*ComposedPrompt, error)
}
```

### Functions

```go
func NewStarshipComposer() *StarshipComposer
func NewFormatBuilder() *FormatBuilder
```

### StarshipComposer Methods

```go
func (c *StarshipComposer) Compose(
    promptStyle *style.PromptStyle,
    extensions []*extension.PromptExtension,
) (*ComposedPrompt, error)
```

### FormatBuilder Methods

```go
func (fb *FormatBuilder) Build(
    promptStyle *style.PromptStyle,
    extensions []*extension.PromptExtension,
) string
func (fb *FormatBuilder) AddSegment(segment style.Segment, moduleRefs []string)
func (fb *FormatBuilder) SkipSegment(segment style.Segment)
func (fb *FormatBuilder) BuildString(suffix string) string
```

### TransitionTracker Methods

```go
func (t *TransitionTracker) BuildTransition(newStartColor string) string
func (t *TransitionTracker) Skip(segment interface{})
func (t *TransitionTracker) SetLastEndColor(color string)
func (t *TransitionTracker) GetLastEndColor() string
```

---

## terminalops/profile

**Import:** `github.com/rmkohlman/MaestroTerminal/terminalops/profile`

### Types

```go
type Profile struct {
    Prompt     *prompt.Prompt
    Plugins    []*plugin.Plugin
    Shell      *shell.Shell
    PromptRef  string
    PluginRefs []string
    ShellRef   string
    ThemeRef   string
}

type GeneratedConfig struct {
    StarshipTOML  string
    ZshrcPlugins  string
    ZshrcShell    string
    ZshrcFull     string
    InstallScript string
}

type Generator struct { /* unexported */ }
```

### Functions

```go
func NewGenerator(pluginDir string) *Generator
func DefaultProfile() *Profile
func MinimalProfile() *Profile
func PowerUserProfile() *Profile
func ParseFile(path string) (*Profile, error)
func Parse(data []byte) (*Profile, error)
```

### Profile Methods

```go
func (p *Profile) HasInlineConfig() bool
func (p *Profile) HasReferences() bool
```

### Generator Methods

```go
func (g *Generator) Generate(p *Profile) (*GeneratedConfig, error)
func (g *Generator) GenerateZshrc(p *Profile) (string, error)
```

### Interfaces

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

---

## terminalops/shell

**Import:** `github.com/rmkohlman/MaestroTerminal/terminalops/shell`

### Types

```go
type ShellType string

const (
    ShellTypeZsh  ShellType = "zsh"
    ShellTypeBash ShellType = "bash"
    ShellTypeFish ShellType = "fish"
)

type Shell struct {
    Env         []EnvVar
    Aliases     []Alias
    Functions   []Function
    PathPrepend []string
    PathAppend  []string
    Options     []string
    History     *HistoryConfig
    Keybindings []Keybinding
    RawConfig   string
}

type EnvVar struct {
    Name        string
    Value       string
    Description string
    Expand      bool
}

type Alias struct {
    Name        string
    Command     string
    Description string
    Global      bool
}

type Function struct {
    Name        string
    Body        string
    Description string
}

type HistoryConfig struct {
    Size           int
    File           string
    IgnoreDups     bool
    IgnoreSpace    bool
    ShareHistory   bool
    ExtendedFormat bool
}

type Keybinding struct {
    Key         string
    Widget      string
    Command     string
    Description string
}

type Generator struct { /* unexported */ }
```

### Functions

```go
func NewGenerator() *Generator
func DefaultZshOptions() []string
func DefaultHistoryConfig() *HistoryConfig
func CommonAliases() []Alias
func DevAliases() []Alias
func GetDefaults() map[string]interface{}
func ParseFile(path string) (*ShellYAML, error)
func Parse(data []byte) (*ShellYAML, error)
```

### Generator Methods

```go
func (g *Generator) Generate(s *Shell) (string, error)
func (g *Generator) WriteToFile(s *Shell, outputPath string) error
```

---

## terminalops/wezterm

**Import:** `github.com/rmkohlman/MaestroTerminal/terminalops/wezterm`

### Types

```go
type WezTerm struct {
    Font        FontConfig
    Window      WindowConfig
    Colors      ColorConfig
    Leader      *LeaderKey
    Keybindings []Keybinding
    KeyTables   map[string][]Keybinding
    TabBar      *TabBarConfig
    Pane        *PaneConfig
    Plugins     []PluginConfig
}

type FontConfig struct {
    Family string
    Size   float64
}

type WindowConfig struct {
    Opacity float64
}

type ColorConfig struct {
    Scheme string
}

type LeaderKey struct {
    Key     string
    Mods    string
    Timeout int
}

type Keybinding struct {
    Key    string
    Mods   string
    Action string
}

type TabBarConfig struct {
    Enabled  bool
    Position string
}

type PaneConfig struct {
    SplitDirection string
}

type PluginConfig struct {
    Name   string
    URL    string
    Config map[string]any
}

type LuaGenerator struct { /* unexported */ }
```

### Functions

```go
func NewWezTerm(name string) *WezTerm
func NewLuaGenerator() *LuaGenerator
func NewParser() Parser
```

### WezTerm Methods

```go
func (w *WezTerm) HasColors() bool
func (w *WezTerm) HasThemeRef() bool
func (w *WezTerm) HasLeader() bool
func (w *WezTerm) HasKeybindings() bool
func (w *WezTerm) HasKeyTables() bool
func (w *WezTerm) HasPlugins() bool
```

### Interfaces

```go
type Generator interface {
    Generate(spec *WeztermSpec) (string, error)
    GenerateFromConfig(config *WezTerm) (string, error)
}

type Parser interface {
    Parse(data []byte) (*WezTerm, error)
    ParseYAML(data []byte) (*WeztermYAML, error)
}

type Store interface {
    Get(name string) (*WezTerm, error)
    List() ([]*WezTerm, error)
    Save(w *WezTerm) error
    Delete(name string) error
    Exists(name string) bool
}

type Library interface {
    Get(name string) (*WezTerm, error)
    List() ([]*WezTerm, error)
    ListByCategory(category string) ([]*WezTerm, error)
    Names() []string
    Categories() []string
    Count() int
}
```

### LuaGenerator Methods

```go
func (g *LuaGenerator) Generate(spec *WeztermSpec) (string, error)
func (g *LuaGenerator) GenerateFromConfig(config *WezTerm) (string, error)
```

### wezterm/library

```go
func NewWeztermLibrary() (*WeztermLibrary, error)

func (l *WeztermLibrary) Get(name string) (*wezterm.WezTerm, error)
func (l *WeztermLibrary) List() ([]*wezterm.WezTerm, error)
func (l *WeztermLibrary) ListByCategory(category string) ([]*wezterm.WezTerm, error)
func (l *WeztermLibrary) Names() []string
func (l *WeztermLibrary) Categories() []string
func (l *WeztermLibrary) Count() int
```
