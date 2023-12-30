
```
func anyfunc() {
	pre := time.Now()
	defer fmt.println(time.Since(pre))

}
```