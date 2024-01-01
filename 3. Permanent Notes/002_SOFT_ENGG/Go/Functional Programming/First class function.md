First-class Functions: In Go, functions are first-class citizens, which means they can be assigned to variables, passed as arguments to other functions, and returned as values from functions.

```
func add(a, b int) int {
    return a + b
}

func subtract(a, b int) int {
    return a - b
}

func multiply(a, b int) int {
    return a * b
}

func main() {
    // Function assigned to a variable
    operation := add
    result := operation(2, 3) // result = 5

    // Function passed as an argument
    calculate := func(op func(int, int) int, a, b int) int {
        return op(a, b)
    }
    result = calculate(subtract, 5, 3) // result = 2

    // Function returned as a result
    funcFactory := func() func(int, int) int {
        return multiply
    }
    operation = funcFactory()
    result = operation(4, 3) // result = 12
}

```