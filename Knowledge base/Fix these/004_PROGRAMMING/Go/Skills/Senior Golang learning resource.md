
1. Function composition: HTTPHandlerFunc does where a function has a method that invokes itself so that it can be abstracted out with an interface.  Go developers didn‘t design the language for it, they discovered this trick only later. IIRC, Rob Pike once said that in an interview.
2. Function options: better for API : https://dave.cheney.net/2014/10/17/functional-options-for-friendly-apis ; https://sagikazarmark.hu/blog/functional-options-on-steroids/
3. Proper dependency injection
4. Accepting objects dependencies as interface in their respective constructor and returning concrete types
5. DAO design pattern for separating data resources client interface from its data access mechanism
6. DI concept / design is different to DI framework / lib
7. Dependency injection: DI in it's simplest form is just providing a dependency in the constructor/method call/whatever - providing it from outside and not relying on the object/method to get it themself - you "inject" it.
8. Data flow programming pattern i.e flow based programming https://en.wikipedia.org/wiki/Flow-based_programming
9. Add linter
10. table driven tests, it is idiomatic and once you have written few of them it becomes a lot easier compared to splitting test scenarios
11. Generic facilitators
12. ECS or Entity component system https://en.wikipedia.org/wiki/Entity_component_system
13. YAGNI: [Keep the complexity dialed back until it is actually needed](https://google.github.io/styleguide/go/guide#principles)
14. Clean Architecture and hexagonal architecture
15. Adapter design pattern: https://bitfieldconsulting.com/golang/adapter
16. Put in your remote state last. Instead, start with a Mock of your remote state so that you can test stateful code without modifying any remote state.
17. Registry design pattern, fan in/out 
18. Inversion of Control IOC or Dependency Inversion for testing
19. 