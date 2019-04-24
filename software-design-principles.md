## Software Design Checklist

- Have you identified what problem are you really trying to solve

- Have you broken it down into independent components and identified *key* dependencies between components 

- Have you inspected your data objects and graphed the flow of data

- Have you analyzed and optimized flow of data to be as unidirectional as possible

- Have you removed as much business logic as you can from data model objects (except if it is part of object own state)

- Have you avoided creating too many classes like manager, co-ordinators, helpers, handlers, provider, executor and instead focused on the foundations: models, views and controllers

- Have you inspected views are not communicating any data upstream and they do not have business logic

- Are you able to quickly describe responsibility of a component/class/method/module and provide a single unambiguous representation of it

- Have you inspected and graped the ownership of objects and inspected their lifecycle

- Have you inspected that your singletons are encouraging one-way data flow, not involved in two-way communication and not necessarily enforcing anyone to take action

- Have you used two-way data flow with discretion

- Have you used inheritance with caution and prefered composition over inheritance

- Have you inspested and abstracted the communication between sub-components

- Have you analyzed that the design is highly cohesive and loosely coupled and components have clearly defined communication boundaries

- Have you inspected the communication boundaries are not poorly selected and you don't have high coupling and low cohesion

- Have you inspected there are no God objects in the design and you don't have high coupling AND high cohesion

-  Have you hidden any domain specific complexity 

-  Have you eliminated any non-essential complexity e.g. concurrency

-  Have you verified that your design can be tested

-  Have you cautiously inspected if you can make your dependencies more loose

-  Is your design open for extension but minimizes modifications by relying upon principles of abstraction and polymorphism

-  Are you using lazy initilization, adapter, factory/builder pattern wherever necessary
