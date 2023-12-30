
Type: #MOC
Tags:
# Clean Architecture

Chapter 1 and 2

[[What is Design and Architecture ?]]

-   Developer gets work done. While engineers focus on building proper design, scalable systems, and maintainable.
-   While starting a project, become a dev first. Start slow and small. Once MVP ready, be engineer and focus on reliability, scalability and maintainability. Also, refactor when you can.
-   [[TDD]] - seems hard in the beginning but produces reliable applications and surprisingly completes the project/features quickly in the long run.
-   Polya’s technique for problem solving.
-   Software has two values - Behaviour and structure.
-   As an engineer, we should focus on architecture as well and not just on implementing the behaviour. Software should be soft to change the behaviour of the machine not hard.
-   Focus on architecture over urgency of features.
-   Create an architecture that allows features and functions to be easily developed, modified and extended.
- Interest vs Productivity ( lowers as project timeline grows )
-   LOC vs Productivity ( decreases a project completed and moves to maintenance stage hence try to write maintainable code when productivity is high )
-   You can be a engineer or a programmer, do not try to be both at the same time.
-   Eisenhower urgency matrix asd\
- This is new


Chapter  3, 4, 5, 6

[[Different programming Paradigms]]


Chapter 7, 8, 9, 10, 11 ( SOLID)

**[[SOLID]]** is an [[OOD]] ( [[Object Oriented Design]] ) pattern introduced by Robert C martin ( Uncle Bob )

**[[Why use SOLID ?]]** 


**Explain [[SOLID]] in one sentence****


**Chapter 12 ( [[Components]] )**
- [[What is a well designed component]]: 
	- well designed components should be able independently deployable and independently developable
- Moores law: Computer speed, memory, and density double every 18 months. This law held from the 1950s to 2000, but then, at least for clock rates, stopped cold.
- [[Linkers]]
	- - Talks about linkers and loaders ( DLL, ELF)
	- Similar to how in windows you can make things work by just putting some ddl into a particular folders

**Chapter 13 ( [[Component Cohesion]]  )**
- [[Components Cohesion]]
	- REP: [[Reuse / Release Equivalence Principle]]
	- CCP: [[Common Closure Principle ( SRP for components )]]
	- CRP: [[Common Reuse Principle]]

**Chapter 14 ( [[Component coupling]])**
- [[Component Coupling]]
	- [[Acyclic dependency principle ( ADP)]]
	- [[Stable dependencies principle SDP]] )
	- [[Stable Abstraction Principle SAP]])
- Relation between [[stability]] ( I ) and [[Abstractness]] ( A )
- 
	- ![[Screenshot 2022-11-25 at 11.55.39 AM.png]]


- [[Zone of Pain]]
	- if component falls here, too ridit, cannot be extended as it is not absctact
	- Database schema falls in zone of pain
		- Volatile, extremely concrete
		- highly depended on
- [[Zone of Uselessness]]
	- A lot of absctractions but not dependency hence useless
- [[Zone of Exclusion]]
-[[ Distance from main sequence]]
	- D = | A+I-1 |
	- D = 0 means component is directly on main sequence
	- D = 1 means component is far away from main sequence
-[[ Good practice:]] Place component on either side of main sequence, or try to be within in 1 SD away from the main sequence.

Chapter 15 ( What is Architecture )
- The architecture of a software system is the shape given to that system by those who build it. The form of that shape is in the division of that system into components, the arrangement of those components, and the ways in which those components communicate with each other.
- The purpose of that shape is to facilitate the development, deployment, operation, and maintenance of the software system contained within it.
- The primary purpose of architecture is to support the life cycle of the system. Good architecture makes the system easy to understand, easy to develop, easy to maintain, and easy to deploy.
- The ultimate goal is to minimize the lifetime cost of the system and to maximize programmer productivity.
- Development
	- A software system that is hard to develop is not likely to have a long and healthy lifetime. So the architecture of a system should make that system easy to develop, for the team(s) who develop it.
	- well-defined components with reliably stable interfaces.
- Deployment
	- define a deployment strategy
	- A goal of a software architecture, then, should be to make a system that can be easily deployed with a single action.
- Operation
	- A good software architecture communicates the operational needs of the system.
	- It’s just that the cost equation leans more toward development, deployment, and maintenance.
	- The architecture of the system should elevate the use cases, the features, and the required behaviors of the system to first-class entities that are visible landmarks for the developers.
- Maintenance
	- The primary cost of maintenance is in spelunking and risk. Spelunking is the cost of digging through the existing software, trying to determine the best place and the best strategy to add a new feature or to repair a defect. While making such changes, the likelihood of creating inadvertent defects is always there, adding to the cost of risk.
	- By separating the system into components, and isolating those components through stable interfaces, it is possible to illuminate the pathways for future features and greatly reduce the risk of inadvertent breakage.
- Keeping option open
	- It is not necessary to choose a database system in the early days of development, because the high-level policy should not care which kind of database will be used. Indeed, if the architect is careful, the high-level policy will not care if the database is relational, distributed, hierarchical, or just plain flat files.
	    
	-   It is not necessary to choose a web server early in development, because the high-level policy should not know that it is being delivered over the web. If the high-level policy is unaware of HTML, AJAX, JSP, JSF, or any of the rest of the alphabet soup of web development, then you don’t need to decide which web system to use until much later in the project. Indeed, you don’t even have to decide if the system will be delivered over the web.
	    
	-   It is not necessary to adopt REST early in development, because the high-level policy should be agnostic about the interface to the outside world. Nor is it necessary to adopt a micro-services framework, or a SOA framework. Again, the high-level policy should not care about these things.
	    
	-   It is not necessary to adopt a dependency injection framework early in development, because the high-level policy should not care how dependencies are resolved.
- A good architect pretends that the decision has not been made, and shapes the system such that those decisions can still be deferred or changed for as long as possible.
- A good architect maximizes the number of decisions not made.
- Device independence
	- 

Chapter 16 ( independence )
- a good architecture must support
	- The use cases and operation of the system.
	- The maintenance of the system.  
	- The development of the system.  
	- The deployment of the system.
- use cases
	- use case means architecture of system must support intent of system
	- If the system is a shopping cart application, then the architecture must support shopping cart use cases.
	- The architecture must support the use cases.
- Operation
	- If the system must handle 100,000 customers per second, the architecture must support that kind of throughput and response time for each use case that demands it
- Development
	- A system that must be developed by an organization with many teams and many concerns must have an architecture that facilitates independent actions by those teams, so that the teams do not interfere with each other during development. This is accomplished by properly partitioning the system into well-isolated, independently developable components. Those components can then be allocated to teams that can work independently of each other.
- Deployment
	- A good architecture does not rely on dozens of little configuration scripts and property file tweaks.
	- The goal is “immediate deployment.”
	- good architecture helps the system to be immediately deployable after build. - CI/CD
	- Again, this is achieved through the proper partitioning and isolation of the components of the system, including those master components that tie the whole system together and ensure that each component is properly started, integrated, and supervised
- Leaving options open
	- We do not know everything, hence a proper architecture should leave optoins open.
	- Those principles help us partition our systems into well-isolated components that allow us to leave as many options open as possible, for as long as possible.
	- A good architecture makes the system easy to change, in all the ways that it must change, by leaving options open.
- Decoupling layers
	- a good architect will want to separate the UI portions of a use case from the business rule portions in such a way that they can be changed independently of each other, while keeping those use cases visible and clear.
	- system divided into decoupled horizontal layers—the UI, application-specific business rules, application-independent business rules, and the database, just to mention a few.
- Decoupling use cases
	- To achieve this decoupling, we separate the UI of the add-order use case from the UI of the delete-order use case
	- We do the same with the business rules, and with the database. We keep the use cases separate down the vertical height of the system.
	- If you decouple the elements of the system that change for different reasons, then you can continue to add new use cases without interfering with old ones.

Chapter 17 ( Boundaries: Drawing lines)
- Boundary - drawing lines
- Separating softwares elements from one another
- Premature decision - That have nothing to do with business requirements
	- The use case of the system 
- Sad story
- When and which lines to draw ?
	- between things that matter and things that don't
- Plugin architecture
- Plugin argument
- Conclusion
	- To draw boundary lines in a software architecture, you first partition the system into components.
	- Some of those components are core business rules; others are plugins that contain necessary functions that are not directly related to the core business.
	- Then you arrange the code in those components such that the arrows between them point in one direction—toward the core business.
	- You should recognize this as an application of the Dependency Inversion Principle and the Stable Abstractions Principle. Dependency arrows are arranged to point from lower-level details to higher-level abstractions.

Chapter 18 ( Boundary Anatomy)

- Boundary crossing
	- function on one site of a boundary calling function on another side and passing some data.
	- Boundaries are not visible on monoliths
	- example is a function from low level client calling higher level services. Both run time dependency and compile time dependency are in same direction.
- Deployment components for architectural boundary
	- DLL in windows
	- or java JAR files
- Threads
	- may be contained in a component or spread across many components
- Local process
	- Strong architectural boundary
- Services
	- stronges boundary
	- conclusion
		- systems use more than one boundaries
		- service boundaries can inherit local process boundary


Chapter 19 ( Policy and level )
- Policy
	- software architecture is carefully separating those policies from one another, and regrouping them based on the ways that they
	- Business requirements
	- Policies that change for the same reasons, and at the same times, are at the same level and belong together in the same component. Policies that change for different reasons, or at different times, are at different levels and should be separated into different components.
- Levels
	- higher level policies
	- lower level policies

Chapter 20 ( Business rules )
-  Critical business rule
- Entities
- Use cases
- Request model and response
- Conclusion

Chapter 21 ( Screaming architecture )
- Architecture should reflect what it is and what it should do ?
- Good architectures are centered on use cases so that architects can safely describe the structures that support those use cases without committing to frameworks, tools, and environments.
- Frameworks are tools, not ways of life
- should have testable architecture
- A good architecture makes it easy to change your mind about those decisions, too. A good architecture emphasizes the use cases and decouples them from peripheral concerns.

Chapter 22 ( Clean architecture )
- Each of these architectures produces systems that have the following characteristics:
	• Independent of frameworks. 
	• Testable. 
	• Independent of the UI. 
	• Independent of the database. 
	• Independent of any external agency. 
- A clean architecture layers
	- Entities
	- Use case
	- Interface adapters
	- Frameworks and drivers


