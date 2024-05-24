## Summary

#### Core Principle
- Aim to balance pragmatism with idealism as there is no such thing as perfect design.

#### Process Blueprint
1. **Learning**: Begin with the best knowledge and resources.
2. **Optimization**: Adjust based on what works and what doesn't.
3. **Almost Perfection**: Use insights to create a balanced, high-quality solution.

#### Software Design Checklist
- Identify the problem clearly.
- Define responsibilities of components concisely.
- Break down into independent components and key dependencies.
- Ensure high cohesion and loose coupling.
- Define clear communication boundaries between components.
- Inspect and abstract sub-component interactions and data flow.
- Prefer unidirectional data flow and avoid excessive two-way data flow.
- Minimize business logic in data model objects.
- Focus on models, views, and controllers, avoiding excess classes like managers or helpers.
- Ensure views do not handle business logic or communicate data upstream.
- Track object ownership and lifecycle.
- Prefer composition over inheritance and avoid "God objects."
- Simplify by eliminating non-essential complexity.
- Make the design testable and open for extension with minimal modifications.
- Use design patterns like lazy initialization, adapter, and factory/builder where needed.
- Handle both system-wide and sub-component failures.
- Avoid over or under-architecting the system.

#### Software Design Notes
- Keep components independent with key dependencies.
- Simplify class interactions and data flow.
- Follow the Single Responsibility Principle for clarity and simplicity.
- Inspect class composition and ownership.
- Manage singletons carefully to avoid complex two-way data flows.
- Use design patterns appropriately (e.g., Chain of Responsibility, Adapter, Factory, Builder).
- Aim for high cohesion and low coupling.
- Emphasize simplicity and focus on solving real problems.

#### Key Principles
- **SOLID Principles**:
  - **SRP (Single Responsibility Principle)**: Each element should handle one task.
  - **OCP (Open Closed Principle)**: Software should be open for extension but closed for modification.
  - **LSP (Liskov Substitution Principle)**: Derived classes should be replaceable with base classes.
  - **ISP (Interface Segregation Principle)**: Avoid relying on interfaces that are too broad.
  - **DIP (Dependency Inversion Principle)**: High-level modules should not depend on low-level modules.

#### Best Practices
- Avoid long methods for better readability, testing, and reuse.
- Focus on cohesive, single-purpose components.
- Implement good design to manage complexity, making the system easier to change and test.
- Apply principles like DRY (Don't Repeat Yourself) to reduce development costs and improve maintainability.

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
* **SOLID Princples** - intended to make software designs more understandable, flexible and maintainable
   * SRP - Single Responsbility Principle
   * OCP - Open Closed Principle
   * LSP - Liskov's Substitution Principle
   * ISP - Interface Segregation Principle
   * DIP - Dependency Inversion Principle
* SRP
   * each programming element (class, object, method) should be responsible for one task appropriate for its level
   * elements should not dictate one or more tasks
   * if there is a complicated task that compromises of multiple smaller tasks, the greater task should be broken down into smaller tasks managed each by an element
   * re-usability is the advantage here
   * if an action needs to be repeated, it will be its own responsible component which is called whenever it is needed (DRY: don't repeat yourself)
* OCP
   * Software module should be upon for extension but closed from modification: abstraction and polymorphism are the key to make this happen
   * Two options to make an enhancement: change existing code (disliked path), add a small new module of code
   * if we want to change a class, we would either extend it or create an entirely new class
   * changing existing component can break compatibility   
* LSP
   * if class A is subtype of B, then anywhere in the program, class B should be replaceable with class A without breaking the program
   * inheritance should be used only for substitutability
   * if an object of B should be used anywhere an object of A is used then use inheritance
   * if an object of B should use an object of A then use composition/delegation
   * inheritance demands more from a developer than composition or delegation does
   * services of the derived class should require no more and promise no less than the corresponding services of the base class. Why? -> The user of a base class should be able to use an instance of a derivced class without knowing the difference
   * Good example of this principle:
      * public vs protected in base vs derived
      * derived function can't throw any new checked exception not thrown by the base (unless the new exceptions extend old one)
* ISP
   * Components in a user interace such as buttons and labels should not be reliant on one another i.e. if one button breaks, the rest of the user interface should not malfunction
* DIP
   * high level components should not depend on low-level components and vice-versa rather they communicate via interfaces/abstraction
   * if one fails the other continues functioning as they are not dependent on each other
   * beause of this we can test classes independently of each other which makes testing easier
   * we can create any kind of mock objects we want

## Credits

- https://enterprisecraftsmanship.com/posts/cohesion-coupling-difference/
