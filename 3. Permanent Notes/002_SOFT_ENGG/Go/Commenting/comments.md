
## Programming 

### Good code has a lot of comments, bad code requires a lot of comments - Dave Thomas and Andrew Hunt

#### Comment

A comment should
- explain what that thing does
- explain how the thing does what it does
- explain why the thing is why it is


##### TODO comment

```go
// TODO(pthapa): This is O(N^2), find a faster way to do this
```

The above provides a username. This does not mean that person has committed to fixing the issue, but they may be the best person to ask when the time comes to address it. Other projects annotate TODO with a date or an issue number


Guides
1. Rather than commenting a block of code, its better to remove or refactor it
2. Good package name should be unique


### Package naming

Avoid package names like base, common or util

My recommendation to improve the name of `utils` or `helpers` packages is to analyse where they are called and if possible move the relevant functions into their caller’s package. Even if this involves duplicating some helper code this is better than introducing an import dependency between two packages.

A little duplication is far cheaper than wrong abstraction - Sandy Metz

