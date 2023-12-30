Functional Patterns: Go can employ various functional programming patterns, such as map, reduce, filter, and recursion. These patterns allow you to process collections of data in a functional and declarative manner.


```
type Item struct {
    Value int
}

func main() {
    numbers := []Item{{Value: 1}, {Value: 2}, {Value: 3}, {Value: 4}, {Value: 5}}

    // Map: Multiply each value by 2
    mapped := make([]Item, len(numbers))
    for i, num := range numbers {
        mapped[i] = Item{Value: num.Value * 2}
    }

    // Filter: Select even numbers
    filtered := make([]Item, 0)
    for _, num := range numbers {
        if num.Value%2 == 0 {
            filtered = append(filtered, num)
        }
    }

    // Reduce: Sum all values
    sum := 0
    for _, num := range numbers {
        sum += num.Value
    }
    // sum = 15
}

```