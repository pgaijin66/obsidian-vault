
A closure is a concept in programming that allows a function to "remember" the scope in which it was created.

Meaning even if the function has finished executing, function can access variables and parameters from its outer function

```
function outerFunction(outerVariable) {
    // This is the outer function scope
    return function innerFunction(innerVariable) {
        // This is the inner function scope
        return outerVariable + innerVariable;
    };
}

const closureExample = outerFunction(5); // closureExample now holds the inner function

const result = closureExample(3); // This will return 8 (5 + 3)
```

In above example

- `outerFunction` is a function that takes one argument, `outerVariable`
- it returns another function called `innerFunction`
- When we call `outerFunction(5)`, it returns `innerFunction` and binds `outerVariable` to the value `5`.
- At this point, `innerFunction` "remembers" that it was created in the scope of `outerFunction`, so it has access to `outerVariable`.
- We assign the returned function (which is `innerFunction`) to the variable `closureExample`.
- At this point, `closureExample` is essentially `innerFunction` with `outerVariable` locked to `5`.
- - Later in the code, when we call `closureExample(3)`, it executes `innerFunction` with `innerVariable` set to `3`.
- 1. - `innerFunction` then adds `outerVariable` (which is `5`) to `innerVariable` (which is `3`), resulting in `8`.

The key concept here is that `innerFunction` "closes over" the environment in which it was created, which includes the `outerVariable`. This means that even though `outerFunction` has finished executing, `innerFunction` still retains access to the variables and parameters of `outerFunction`

In this case, `innerFunction` can access `outerVariable` because it was created within the scope of `outerFunction`. This is why it's called a "closure" - it "closes over" the environment it was created in.