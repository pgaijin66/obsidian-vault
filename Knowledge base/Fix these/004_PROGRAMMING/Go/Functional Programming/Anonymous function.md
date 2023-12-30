Anonymous Functions (Closures): Go supports anonymous functions or closures, which are functions that are defined without a name. Closures can capture variables from the surrounding lexical scope, allowing for a functional programming style.


```
func main() {
    x := 5
    multiplyByX := func(n int) int {
        return n * x
    }
    result := multiplyByX(3) // result = 15
}

```