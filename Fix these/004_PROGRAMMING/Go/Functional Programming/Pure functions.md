Pure Functions: Although Go does not enforce purity, you can still write pure functions in Go. Pure functions do not modify external state, have no side effects, and produce the same output for the same input.

```
func add(a, b int) int {
    return a + b
}

func main() {
    result := add(2, 3) // result = 5
}

```