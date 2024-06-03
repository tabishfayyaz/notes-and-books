## Summary

**General Guideline:**
- There is no perfect design. Aim to balance pragmatism with idealism.

**Process Blueprint:**
1. **Learning**: Start with the best knowledge and resources.
2. **Optimization**: Adjust based on successes and failures.
3. **Almost Perfection**: Balance quality and practicality with findings and experiences.

**Software Design Checklist:**
- Identify the core problem you're solving.
- Clearly define the responsibility of components/classes/methods/modules.
- Break down into independent components and identify key dependencies.
- Ensure high cohesion, loose coupling, and clear communication boundaries.
- Abstract communication between sub-components.
- Graph the flow of data and inspect data objects.
- Aim for unidirectional data flow.
- Ensure singletons encourage one-way data flow.
- Remove business logic from data model objects, unless it's part of the object's state.
- Avoid overuse of classes like manager, coordinators, helpers, handlers, providers, executors.
- Ensure views do not communicate data upstream or contain business logic.
- Inspect object ownership and lifecycle.
- Prefer composition over inheritance.
- Avoid God objects with high coupling and cohesion.
- Hide domain-specific complexity and eliminate non-essential complexity.
- Ensure your design is testable.
- Design should be open for extension but minimize modifications.
- Use lazy initialization, adapter, factory/builder patterns as necessary.
- Plan for system and sub-component failure.
- Avoid over or under-architecting the system.

**Software Design Notes:**
- Break into independent components with key dependencies.
- Keep data flow and class interactions simple.
- Minimize the number of class types (controllers, views, data objects).
- Keep business logic out of data model objects.
- Single Responsibility Principle: Classes should have a straightforward, single responsibility.
- Inspect class composition and object ownership.
- Ensure singletons are scoped and testable.
- Prefer one-way data flow in singletons (Publisher/Subscriber pattern).
- Use Chain of Responsibility pattern with discretion.
- Use composition over inheritance to avoid tight coupling.
- Lazy initialization boosts startup performance.
- Adapter Pattern standardizes different APIs.
- Factory Pattern wraps constructors for single method calls.
- Builder Pattern allows gradual parameter buildup for constructors.
- Aim for high cohesion and low coupling.
- Simplicity makes code easier to change and understand.

**Cohesion vs Coupling:**
- High Cohesion, Low Coupling is ideal.
- Complexity makes code difficult to change.

**Good Design:**
- Focuses on real problems, is easier to verify, and minimizes accidental complexity.
- Ensures the system is cohesive and loosely coupled.
- Uses DRY (Don't Repeat Yourself) to reduce development costs.

**Focus on Single Responsibility:**
- Define the primary responsibility of a class/method.
- Avoid long methods as they are hard to test, remember, read, and reuse.
- Use SLAP (Single Level of Abstraction Principle).
- Comment on why, not what.
- Use compose method pattern for clarity.

**SOLID Principles:**
1. **Single Responsibility Principle (SRP)**: One class, one responsibility.
2. **Open/Closed Principle (OCP)**: Open for extension, closed for modification.
3. **Liskov Substitution Principle (LSP)**: Subtypes should be replaceable for their base types.
4. **Interface Segregation Principle (ISP)**: Specific interfaces over general ones.
5. **Dependency Inversion Principle (DIP)**: Depend on abstractions, not concretions.


## Software Design Principles

*There is no such thing as perfect design so aim to balance pragmatism with idealism.*

### Process Blueprint 

- Stage 1 - Learning: start the work based on your best knowledge and resources
- Stage 2 - Optimization: change things based on what worked and what didn't work
- Stage 3 - Almost Perfection: Use your findings & experiences to build something that balances quality and reality

### Software Design Checklist

- Have you identified what problem are you really trying to solve

- Are you able to quickly describe responsibility of a component/class/method/module and provide a single unambiguous representation of it

- Have you broken it down into independent components and identified *key* dependencies between components 

- Have you analyzed that the design is highly cohesive, loosely coupled and components have clearly defined communication boundaries that are not poorly selected

- Have you inspected and abstracted the communication between sub-components

- Have you inspected your data objects and graphed the flow of data

- Have you used two-way data flow with discretion so that flow of data is as unidirectional as possible

- Have you inspected that your singletons are also encouraging one-way data flow and not necessarily enforcing anyone to take action

- Have you removed as much business logic as you can from data model objects (except if it is part of object own state)

- Have you avoided creating too many classes like manager, coordinators, helpers, handlers, provider, executor and instead focused on the foundations: models, views and controllers

- Have you inspected views are not communicating any data upstream and they do not have business logic

- Have you inspected and graphed the ownership of objects and inspected their lifecycle

- Have you used inheritance with caution and prefered composition over inheritance

- Have you inspected there are no God objects in the design and you don't have high coupling AND high cohesion

- Have you hidden any domain specific complexity 

- Have you eliminated any non-essential complexity e.g. concurrency

- Have you verified that your design can be tested

- Is your design open for extension but minimizes modifications by relying upon principles of abstraction and polymorphism

- Are you using lazy initilization, adapter, factory/builder pattern wherever necessary

- Have you analyzed and handled when the whole system fails (irrespective of the reason)

- Have you analyzed and handled the case when any sub-component of the system fails (irrespective of the reason)

- Have you examined that you have not over architectured or under architectured the system

### Software Design Notes

* Breakup into independent components yet have key dependencies so what you are doing doesn't impact all the others dependent on your compoonent/module/library

* Inspect your data objects and graph their interactions/where they flow. Keep classes simple. Keep one-way data flow/interactions simple

* Simplicity - keep a small number of class types (controllers, views, data objects) avoid creating classes that aliases those classes (managers, coordinators, helpers, handlers, provider, executor). Keep data flow simple e.g. don't let views have business logic; views shouldn't need to communicate any data upstream

* Keep data model objects pruned of business logic, except if the logic is part of the objects own state. Remove as much business logic as you can.

* Single Responsibility Principle -> If you can't describe a class's responsibility straight forwardly, it's too complex (makes it easy to reason about things)

* Inspect class composition (follow ownership graph). Follow the life of a child (composed) object to make sure that accesibility to and operations done on that object is managed.

* Follow ownership graph -> which objects own which object? It is interesting to know as generally that is how data should flow as well, data should go from parent to child. If you have two parents modifying a single child there could be a conflict, easily given rise to bugs and may also have memory leaks.

* Singletons can introduce a lot of two way data flow e.g. imagine the graph of 100 singletons (very complex). Might make sense for them to not communicate directly but issue notifications that other singletons can listen to, that would at-least take care of 2-way data flow so data is always flowing downwards from central notification hub and singleton are passively broadcasting but not expecting anyone to take action on that

* Singletons: globals floating in the system. Use dependency injection to scope singleton and make them testable. Singletons often represent independent (uncoupled) objects which is good for representing independent execution flow.

* One variation is Singleton could expose Publisher/Subscribe interface (one publisher object, many subscriber object listening to events), encourages nice one-way data flow. Delegate pattern in iOS is variation of this.

* Chain of Responsbility Pattern: Hierarchical data flow - unhandled events bubble upwards to parent objects who should handle them, conflict with the idea of keeping one-way data flow so use with discrection

* OOP inheritance can create tightly coupled hierarchical objects that are hard to refactor (object dependencies). Use composition pattern to allow flexibility in what the container object can do.

* Lazy initialization -> startup performance boost

* Adapter Pattern: You could have many different types of objects with different methods and API, and then using an adapter you can have them all match a certain set of APIs and interfaces so they expose a common set of operations

* Factory Pattern: Wrapper function around a constructor (possibly one in a different class). The key difference is that a factory method pattern requires the entire object to be built in a single method call, with all parameters pass in on a single line. The final object will be returned e.g. `FruitFactory.create(params)`

* Builder Pattern: is in essence a wrapper object around all possible parameters you might want to pass into a constructor invocation. This allows you to use setter methods to slowly build up your parameter list. One additional method on a builder class is a build() method, which simply passes the builder object into the desired constructor, and returns the result e.g. `new FruitBuilder().setX().setY().setZ().build()`

* Cohesion is the indication of the relationship with a module

* Coupling is the indication of the relationship between modules

* Impossible to achieve full decoupling without damaging cohesion and vice versa

![Cohesion vs Coupling](https://github.com/tabishfayyaz/book-club/raw/master/images/coupling-cohesion.png)

- **IDEAL** -> High Cohesion, Low Coupling

![Cohesion vs Coupling Graph](https://github.com/tabishfayyaz/book-club/raw/master/images/cohesion-coupling-graph.png)

* Good Design -> Easier to change but almost impossible to get it done right first time
* Complexity -> Causes code difficult to change
* Simple
    * Keeps you focused
    * Solves only real problem
    * Fails less
    * easier to understand
 
* Ask -> What problem are you really trying to solve?
* Good Design -> hides inherent (domain based e.g. Android) complexity and elimiates accidental (e.g. concurrency) complexity
* When should I implement something?
* How much do you know?
* Cost of implementing:
    * Now vs Later (Postpone is not procrastination)
    * $N  >  $L  -> postpone
    * $N  ==  $L -> postpone
    * $N  <  $L  -> postpone (how probably? high -> do it now, low -> postpone)
* Good design -> easy to verify (automated testing)
* If cohesive -> it has to change less frequently as we want software to change, but not too expensive
* Our homes are highly cohesive and low coupled e.g. things that are for kitchen stay in kitchen
* Worst form of coupling -> inheritance, try to see if you can remove coupling
* Dependencies -> knock out before you mock out however can't remove all the dependencies
    1. get rid of it
    2. make it loose instead of tight
* Depending on a class is tight coupling
* Depending on an inheritance is loose coupling (use caution)
* Every piece of knowledge in a system should have single unambiguous representation
* DRY (don't repeat yourself) -> reduces cost of development
* There are tools to find duplication -> CPD, Semiyen
* Focus on single responsibility:
   * Ask the purpose of a class/method
   * if answer is this class/method does X and Y and Z, the 'and' is giving you clues that it is probably doing way too much
   * define primary responsbility of a class/method
* Long methods are bad, why:
   * hard to test
   * hard to remember
   * hard to read
   * many reasons to change
   * hard to reuse so leads to duplication
   * can't be optimized by anything
   * lack cohesion, high couplin
   * hard to debug
   * How long is long method? Do SLAP: Single Level of Abstraction Principle
* Don't comment what, instead comment why
* Compose method pattern -> focus on story as oppose to minor details

The **SOLID principles** are a set of five design principles intended to make software designs more understandable, flexible, and maintainable. They are:

1. **Single Responsibility Principle (SRP)**: A class should have only one reason to change, meaning it should have only one job or responsibility. This makes the class easier to maintain and understand.

2. **Open/Closed Principle (OCP)**: Software entities (classes, modules, functions, etc.) should be open for extension (polymorphism, inheritance enables that) but closed for modification. This means you should be able to add new functionality without changing existing code.

3. **Liskov Substitution Principle (LSP)**: Objects of a superclass should be replaceable with objects of a subclass without affecting the correctness of the program. This ensures that derived classes extend the base class without altering its behavior.

4. **Interface Segregation Principle (ISP)**: No client should be forced to depend on interfaces it does not use. This encourages the creation of more specific interfaces rather than a large, general-purpose one.

5. **Dependency Inversion Principle (DIP)**: High-level modules should not depend on low-level modules. Both should depend on abstractions. Additionally, abstractions should not depend on details. Details should depend on abstractions. This principle helps reduce the coupling between different parts of the code.
 
1. **High-level modules should not depend on low-level modules. Both should depend on abstractions.**
2. **Abstractions should not depend on details. Details should depend on abstractions.**

This principle helps reduce the coupling between different parts of the code by ensuring that both high-level and low-level modules depend on abstractions rather than concrete implementations.

**Why DIP?**

- **Flexibility**: Code becomes more flexible and easier to change or extend.
- **Testability**: Code is easier to test because dependencies can be swapped out with mocks or stubs.
- **Maintainability**: High-level modules are not affected by changes in low-level modules.

## Credits

- https://enterprisecraftsmanship.com/posts/cohesion-coupling-difference/
