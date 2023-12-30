
#### Literal vs declaration
It is generally good practice to initialize variable using `var cfg Configuration` as you want the zero value . I

Good `var cfg Configruation`
bad `cfg := Configuration{}`

If you explicitly settings fields during declaration then use the literal form

#### Module
Module name should not use canonical SCM path. It should be `github.com/ORG/REPO`

#### Pointers

When to use pointers in Go ?
Use pointers only when you need shared mutability of data contained


#### Functions
Functions are first class citizens in Go. We refer to them by name without `()` and then pass that around or invoke it later on


#### Constructor
In go we do not prefer using constructor just for the sake of using constructor.  If you can work with struct and its data directly, its better to prefer that

#### Package naming
No underscore in package naming unless `foo_test` which is used by go test command


