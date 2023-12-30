A
#### Time execution time

```
func anyfunc() {
	pre := time.Now()
	defer fmt.println(time.Since(pre))

}
```


#### Pre-allocating slices

```
// bad
a := make([]int,10)

//good
b := make([]int, 0, 10)
b = append(b,1)
```


Combining multiple errors

```
err1 := errors.New("error 1")
err2 := errors.New("error 2")

err := err1
err := errors.Join(err, err1)

fmt.Println(errors.Is(err, err1)) //true
fmt.Println(errors.Is(err, err2)) //true

```
