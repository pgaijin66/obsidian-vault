
```
function 1(arg1 strng) {

	return function2(){
		print"arg1"
	}
}

result = function1("hello")

result()
```

- closure is a programming technique where you effectively close over the function to a particular value encapsulating the underlying implementation
- Used for privacy, security, memory efficiency, [[Currying]]
- this is possible due to lexical scoping where any variable defined within that lexical code can be accessed by function defined inside that scope