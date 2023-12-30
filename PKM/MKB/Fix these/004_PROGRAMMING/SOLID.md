
**S : [[Single Responsibility Principle SRP]]  **-> A class / function / method should do one and only on thing. ( eg: Just because you can put everything in a single method, does not mean you should )  

**O: [[Open / Close principle OCP]]-> Entities should be open to extension but closed for modification. ( eg: a class / function / method should be extended using OOP features like polymorphism , inheritance instead of modifying the actual code of the class  )  

**L: [[Liskov Substitution Principle** LSP]]-> Objects in program should be replaceable with their instance of their subtypes without altering correctness of the program. ( eg:  your super class should be substitutable with its objects or subclasses without affecting the program. if it causes error, then the implementation is wrong and abstraction was not done properly )  

**I:[[ Interface Segregation Principle ISP]])** -> **Do not use interface / methods that it does not need.  ( eg: objects should not be hooked to methods or functions that they do not use )**  

**D : [[Dependency Inversion Principle DIP]] **-> Entities must depend on abstraction not concretion / Talks about decoupling. ( eg: a getdata method from database should not be dependent on how database engine was setup and initialized ,  you should be able to swap database under the hood without affecting or changing get data method). abstractions Interfaces and the concretions Implementations