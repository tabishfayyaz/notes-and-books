*There is no such thing as perfect design so aim to balance pragmatism with idealism.*

## Process Blueprint 

- Stage 1 - Learning: start the work based on your best knowledge and resources
- Stage 2 - Optimization: change things based on what worked and what didn't work
- Stage 3 - Almost Perfection: Use your findings & experiences to build something that balances quality and reality

## Software Design Checklist

- Have you identified what problem are you really trying to solve

- Are you able to quickly describe responsibility of a component/class/method/module and provide a single unambiguous representation of it

- Have you broken it down into independent components and identified *key* dependencies between components 

- Have you analyzed that the design is highly cohesive, loosely coupled and components have clearly defined communication boundaries that are not poorly selected

- Have you inspested and abstracted the communication between sub-components

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

## Software Design Notes

- Breakup into independent components yet have key dependencies so what you are doing doesn't impact all the others dependent on your compoonent/module/library
- Inspect your data objects and graph their interactions/where they flow. Keep classes simple. Keep one-way data flow/interactions simple
- Simplicity - keep a small number of class types (controllers, views, data objects) avoid creating classes that aliases those classes (managers, coordinators, helpers, handlers, provider, executor). Keep data flow simple e.g. don't let views have business logic; views shouldn't need to communicate any data upstream
- Keep data model objects pruned of business logic, except if the logic is part of the objects own state. Remove as much business logic as you can.
- Single Responsibility Principle -> If you can't describe a class's responsibility straight forwardly, it's too complex (makes it easy to reason about things)
- Inspect class composition (follow ownership graph). Follow the life of a child (composed) object to make sure that accesibility to and operations done on that object is managed.
- Follow ownership graph -> which objects own which object? It is interesting to know as generally that is how data should flow as well, data should go from parent to child. If you have two parents modifying a single child there could be a conflict, easily given rise to bugs and may also have memory leaks.
- Singletons can introduce a lot of two way data flow e.g. imagine the graph of 100 singletons (very complex). Might make sense for them to not communicate directly but issue notifications that other singletons can listen to, that would at-least take care of 2-way data flow so data is always flowing downwards from central notification hub and singleton are passively broadcasting but not expecting anyone to take action on that
- Singletons: globals floating in the system. Use dependency injection to scope singleton and make them testable. Singletons often represent independent (uncoupled) objects which is good for representing independent execution flow.
- One variation is Singleton could expose Publisher/Subscribe interface (one publisher object, many subscriber object listening to events), encourages nice one-way data flow. Delegate pattern in iOS is variation of this.
- Chain of Responsbility Pattern: Hierarchical data flow - unhandled events bubble upwards to parent objects who should handle them, conflict with the idea of keeping one-way data flow so use with discrection
- OOP inheritance can create tightly coupled hierarchical objects that are hard to refactor (object dependencies). Use composition pattern to allow flexibility in what the container object can do.
- Lazy initialization -> startup performance boost
- Adapter Pattern: You could have many different types of objects with different methods and API, and then using an adapter you can have them all match a certain set of APIs and interfaces so they expose a common set of operations
- Factory Pattern: Wrapper function around a constructor (possibly one in a different class). The key difference is that a factory method pattern requires the entire object to be built in a single method call, with all parameters pass in on a single line. The final object will be returned e.g. `FruitFactory.create(params)`
- Builder Pattern: is in essence a wrapper object around all possible parameters you might want to pass into a constructor invocation. This allows you to use setter methods to slowly build up your parameter list. One additional method on a builder class is a build() method, which simply passes the builder object into the desired constructor, and returns the result e.g. `new FruitBuilder().setX().setY().setZ().build()`
- Cohesion is the indication of the relationship with a module
- Coupling is the indication of the relationship between modules
- Impossible to achieve full decoupling without damaging cohesion and vice versa



- **IDEAL** -> High Cohesion, Low Coupling

## Credits
- https://enterprisecraftsmanship.com/posts/cohesion-coupling-difference/
