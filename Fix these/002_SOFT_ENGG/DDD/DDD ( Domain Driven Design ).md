
Here are the 9 most important DDD patterns. 👇  
  
1. Bounded Contexts  
  
Instead of having one large and complex domain - you split it into smaller, more manageable parts.  
  
Each part has its distinct model and boundaries.  
  
Bounded contexts prevent conflicts and confusion by isolating different parts of the domain.  
  
2. Anti-Corruption Layer:  
  
Protects a bounded context from outside influence. These can be external systems or domains that use different models.  
  
It ensures that the internal model remains clean and unaffected.  
  
3. Entities  
  
An entity in the domain:  
- Has a distinct identity  
- Can change over time  
  
Entities are the core building blocks of a domain model.  
  
4. Value Objects  
  
Value objects:  
- Immutable  
- No distinct identity  
- Represented by values  
  
We use Value objects to model properties of entities or other domain concepts.  
  
5. Aggregates  
  
Aggregates group related entities and value objects into a single unit of consistency.  
  
The root entity is a gateway to access and change the objects inside.  
  
Its purpose is to maintain data consistency within a bounded context.  
  
6. Domain events  
  
Domain events represent significant state changes within the domain.  
  
They communicate changes and trigger actions within the application or between bounded contexts.  
  
Important: naming in past tense.  
  
7. Factories  
  
Factories are responsible for creating complex domain objects.  
  
They encapsulate the logic required to create these objects and ensure they are in a valid state.  
  
8. Services  
  
Domain services encapsulate domain logic that doesn't belong to any entities (or value objects).  
  
They are stateless and operate on the domain model to perform specific tasks.  
  
9. Repositories  
  
Repositories provide a way to access and manage aggregates (or entities).  
  
They are a bridge between the application code and the data storage - allowing for a domain-centric approach to data access.