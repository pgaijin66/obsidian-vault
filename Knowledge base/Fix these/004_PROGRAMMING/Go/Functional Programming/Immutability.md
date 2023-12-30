Immutability: While Go does not have built-in immutability features, you can emulate immutability by following certain conventions and best practices. For example, you can use pointers to ensure that a value is not modified or create new instances instead of modifying existing ones.

```
type Point struct {
    X, Y int
}

func main() {
    p := Point{X: 2, Y: 3}
    // Creating a new instance instead of modifying existing one
    q := Point{X: p.X + 1, Y: p.Y} // q = {X: 3, Y: 3}
}

```