

```
// Abstract product
type Button interface {
    Render() string
}

// Concrete product: LightButton
type LightButton struct{}

func (b LightButton) Render() string {
    return "Rendering light theme button."
}

// Concrete product: DarkButton
type DarkButton struct{}

func (b DarkButton) Render() string {
    return "Rendering dark theme button."
}

// Abstract factory
type UIFactory interface {
    CreateButton() Button
}

// Concrete factory: LightThemeFactory
type LightThemeFactory struct{}

func (f LightThemeFactory) CreateButton() Button {
    return LightButton{}
}

// Concrete factory: DarkThemeFactory
type DarkThemeFactory struct{}

func (f DarkThemeFactory) CreateButton() Button {
    return DarkButton{}
}
```