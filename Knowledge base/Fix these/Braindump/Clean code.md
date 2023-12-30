Chapter 1: we 

Chapter 2: Meaningful names
- use intention revealing name 
	- Should reveal what is, why, how
- Avoid disinformation
	- `accountGroup` or `accounts` over `accountList`
- Make meaningful distinctions
	- `user` which has subnet `userFirstName`, `userLastName`  . `FirstName` and `LastName` are clear distinction
- use pronounceable name
	- `sum` over `s`
	- `calculatePrincipleInterest` over `cpi`
- use searchable names
	- Length of name should correspond to the size of its scope
- Avoid encoding
	- Do not use encoding
	- `sum` over `s+` to represent sum
- Avoid additional mental burden
	- `timeOutValueInMs = 30000` over `timeOutValueInMs = 30 * 60 * 60` as it adds additional mental burden to user to calculate the value and also lowers additional performance
- Member prefix
	- Do not use prefix `u_firstName` as it hampers readability and makes it difficult to remember
- Interfaces and implementation
	- Do not use `I` or `A` for interfaces 
- Avoid mental mapping
	- clarity is king
	- difference between smart programmer and professional programmer is that professional understands that clarity is king
	- Write code so that others can understand
- Class names
	- Class or object should have a noun or a noun phrase. It should not be a verb
- Method name
	- method should have a verb or a verb phrase. `calculatePrincipleInterest`
	- if method is 
		- Accessors = should prefix with `get` or `fetch` eg: `getUser`,`fetchUser`
		- Mutator = should have prefix with `set`, `update` eg: `setName`, `updateUserDetail`
		- Predicates ( checks ) = should have prefix with `is` or `check` eg: `CheckAdmin`
- Don't be cute
	- Say what you mean and mean what you say
	- `gracefulShutdown` over `whackThisProgram`
- Don't pun
	- avoid using same word for two different purpose
	- do not do this` name = getUserName("name") and overriding name = getProductName("name")` , better `userName, productName`
- Use solution domain names
	- if you are populating fifo queue of variables, name variable as `Queue`
- Use problem domain names
	- Use problem name if no useful name if found
	- eg: `determineMaxNumber`
- Add meaningful context
	- `determineMaxNumer(1,2,3,4,5)` over `MaxNumer(1,2,3,4,5)`

Chapter 3: Functions
- First rule in creating function is that should be small
	- Second rule is that it should be smaller than that. 
	- Generally from `10-12 lines`  
- Should do one thing and one thing well
	- SRP ( Single Responsibility Principle)
- Use descriptive names
	- As above
- Function arguments, the fewer the better
	- If the number of argument is larger than 3, there is something wrong in the code 
	- Try to use DIP ( Dependency Inversion Principle )
- Should not have any side effects
	- easy to use, hard to misuse
	- `updateUserPhoneNumber`  function
		- updates user phone number in database
		- side effect = it also updates the last modified timestamp
		- if side effect is present, try to avoid it or else document it by renaming the function name as `updateUserPhoneNumberAndLastModifiedTimestamp`
		- Using `And` is not a good practice but to incorporate such `temporal coupling or side effects` it is recommended
- Do not use flag arguments, split method into several independent methods that can be called from the client without the flag.
	- newServer(flags ...opts) = https, TLS, port etc
	- `newServerWithHttps`
	- `newServerWithTLS`
	- `newServerWithPort`
	- `newServer`
- Function should do something or answer something but not both
	- SOLID = S
- DRY
	- Do not repeat yourself

Chapter 4: Comments

- Good comments do not make up for bad code
- Comments lie
	- Try write good readable code which functions as a documentation rather than writing comment.
- Explain yourself in code
	- Why - rationale
- Good comments
	- Legal comments - License
	- Explain things that cannot be expressed in comment
		- Informative comments
		- Explanation of Intent
		- Clarification
	- Warning of consequence
	- TODO comments
- Never keep commented out code in the codebase
- Bad comments
	- Mumbling
	- Redundant comment
	- Misleading comments
	- mandated comments
	- Journal comments
	- Noise comments
	- Scary noise
	- Don't use a comment when you can use a function or a variable
	- Position markers
	- Attribution and bylines
	- commented out code
	- HTML comments
	- Nonlocal information
	- Too much information
	- Inobvious connection
	- Function headers

Chapter 5: Formatting
- Neatness and consistency
	- Team should agree on a code formatting style ( tabs vs spaces etc ) 
	- Style guide
- Vertical formatting
	- Make sure each file is as small as it can be.
	- Add visual openness between concepts 
		- line break
	- Vertical density
		- Code that are tightly related should appear vertically dense. Together
		- No comments between tightly related code block
	- vertical distance
		-  Concepts that are closely related together should be kept vertically close to each other within same file
	- Vertical declaration
		- Local variables should be declared as close to each other as possible
		- Instance variable should be declared at the top of the class
	- Dependent function
		- If a function calls another function, they should be vertically close and the caller should be above the callee if possible
- Horizontal formatting
		- Lines should be only wide that you can see the while line without having to scroll the window(120-150)
		- Horizontal openness and density
			- Use horizontal white spaces to associate things that are strongly related and disassociate things that are weakly related
		- Indentation
			- Use indentation to define scope


Chapter 7 Error handling
Techniques that you can use to write code, that is both clean and robust and handles errors with grace and style.
1. Use exceptions instead of return codes to avoid clutter and improve code readability.
2. Write try-catch-finally statements first to define expected behavior and handle errors effectively.
3. Prefer unchecked exceptions over checked exceptions to avoid violating the Open/Closed principle.
4. Provide context with exceptions by including informative error messages and relevant details.
5. Define exception classes based on the caller's needs, using different classes only when necessary.
6. Implement the Special Case pattern to handle exceptional behavior and encapsulate it in a separate object.
7. Avoid returning null values to prevent potential issues and NullPointerExceptions; consider throwing exceptions or using special case objects instead.
8. Don't pass null values by default to avoid unexpected behavior and potential errors; prohibit null values if possible.


Chapter 8 Boundaries
- When using third-party code, providers aim for broad applicability while users prefer a focused interface tailored to their needs, so wrapping the code in a separate class can simplify future contract changes.
- Exploring third-party APIs involves writing learning/boundary tests to understand the code and detect potential breaking changes with new library releases.
- When dealing with code that is still under development, creating a wrapper or interface allows you to unblock yourself and test interactions with the unfinished API, which can later be replaced by the actual implementation.


### Introduction

In chapter 11: System, Uncle Bob teaches us how to build a clean scalable system.

### Separation of Main

We have to separate startup construction logic and normal runtime processing.

- The main function should build the objects necessary for the system, then pass them to the application, which simply uses them.
- The application has no knowledge of main or of the construction process. It simply expects that everything has been built properly.

### Dependency Injection

Sometimes the application has to be responsible for when an object gets created. An object should not take responsibility for instantiating dependencies itself.

- Abstract Factory Pattern. It gives the application control of when to build the object, but keep the details of that construction separate from the application code (the interface will be implemented by main).
- Dependency Injection. The class takes no direct steps to resolve its dependencies; it is completely passive. Instead, it provides setter methods or constructor arguments (or both) that are used to inject the dependencies (it's handled by a framework and objects won't be constructed until needed).

### Scaling Up

- It is a myth that we can get systems “right the ﬁrst time”. Instead, we should implement only today’s stories, then refactor and expand the system to implement new stories tomorrow.
- We can start a software project with a “naively simple” but nicely decoupled architecture, delivering working user stories quickly.
- It is economically feasible to make radical change, if the structure of the software separates its concerns effectively.
- It is possible to add more infrastructure as we scale up.

Some of the world’s largest Web sites have achieved very high availability and performance, using sophisticated data caching, security, virtualization,etc. All done efﬁciently and ﬂexibly because the minimally coupled designs are appropriately simple at each level of abstraction and scope.

### Optimize Decision Making

It is best to postpone decisions until the last possible moment. A premature decision is a decision made with sub-optimal knowledge. We will have that much less customer feedback, mental reﬂection on the project, and experience with our implementation choices if we decide too soon.

The agility provided by a system with modularized concerns allows us to make optimal, just-in-time decisions, based on the most recent knowledge. The complexity of these decisions is also reduced.

### Standards

Use the simplest thing that can possibly work when designing systems or modules.

Standards make it easier to reuse ideas and components, recruit people with relevant experience, encapsulate good ideas, and wire components together. However, the process of creating standards can sometimes take too long for industry to wait, and some standards lose touch with the real needs of the adopters they are intended to serve.

### Systems Need Domain-Speciﬁc Languages (DSL)

- A good DSL minimizes the “communication gap” between a domain concept and the code that implements it.
- If you are implementing domain logic in the same language that a domain expert uses, there is less risk that you will incorrectly translate the domain into the implementation.

### Conclusion

Systems must be clean too. An invasive architecture overwhelms the domain logic and impacts agility.

- Construction logic should be put in the main function separated from the application logic by using Abstract Factory Pattern and Dependency Injection.
- The system can go from simple to complex if the design is/stay minimally coupled and concerns are separated.
- You should postpone decisions as much as possible to as much knowledge as possible.
- Use the simplest thing that can possibly work - Only follow standards when they really help you.
- Use Domain-Specific Language so the code speaks the same way as experts do and nothing is lost in translation.

When the domain logic is obscured, quality suffers because bugs ﬁnd it easier to hide and stories become harder to implement. If agility is compromised, productivity suffers and the beneﬁts of TDD are lost.



Chapter 13 Concurrency
- 1. A mutex is a synchronization primitive used to protect shared resources by allowing only one thread to access the resource at a time, ensuring mutual exclusion.
2. A lock is a synchronization mechanism that provides exclusive access to a resource, allowing a thread to acquire the lock and proceed while blocking other threads from accessing the resource until it is released.
3. A thread semaphore is a synchronization object that controls access to a shared resource by allowing a specific number of threads to access it simultaneously while blocking others from accessing it until a thread releases the semaphore.
- Concurrency vs parallelism
	- Parallelism: Multiple task executed at the same time
	- Concurrency: Does not necessarily mean tasks are executed at the same time, it focuses on managing and coordinating multiple tasks or process allows them to make process more independent. techniques: task interleaving or time slicing
	- Time slicing: 
		- divides CPU time into small interval or slices allocating each slice to different tasks or process in RR fashion
		- each task is given a task
		- once time is up, kernel halts current state of the process and records its state
		- Kernel them control of the CPU
		- Now next task in CPU is given time. Once done
		- same is repeated, and previous CPU can continue its task
- Why concurrency
- Mutex, locks, semaphore
- Myths and misconceptions
	- concurrency does not add performance
	- design does not change when writing concurrent program
	- understanding concurrency issues
- Challenges
- Concurrency defence principles
- SRP
- Corollary
	- Limit data scope
	- Use copies of Data
	- Threads should be as independent as possible
- Know your library
	- thread safe collection
	- non blocking solutions
- What does thread safe mean
- Know your execution models
- Keep synchronized sections small
- Beware of dependencies between synchronized methods
- Writing correct shut down code is hard
- testing thread code
- test spurious failure as candidate threading issues
- get your non thread code working first
- make your threaded code tunable
- 