## Effective Java

by Joshua Bloch

I own this book and made these notes for my own learning. Please [purchase the book](https://www.amazon.com/Effective-Java-2nd-Joshua-Bloch/dp/0321356683) and support the author.

### Creating and Destroying Objects
* Use static factory instead of constructors.
* Consider a builder when faced with many constructor parameters.
* Enforce singleton property with a private constructor or an enum type.
* Enforce noninstantiability (e.g. UtilityClass) with a private constructor: raise exception inside constructor, in case it is accidentally called.
* Avoid creating unnecessary object: boxing/unboxing inside a loop, new String("xyz") inside a loop. Immutable objects are great candidates for re-using.
* Eliminate all obselete object references: returning object from a stack, hold weak reference to callbacks/listener, don't hold an object in a cache beyond use, *WeakHashMap*. An entry in a WeakHashMap will automatically be removed when its key (not the value) has no longer references outside of the WeakHashMap (cache).
* Avoid finalizers, *finally()* and *try-finally*: never do anything time-critical or depend on a finalizer to update critical persistent state. Require clients invoke explicit termination methods and as *safety-net* call the termination method inside try-finally and log a warning if resource has not been terminated. Invoke super class finalizer from **finally** block. Consider *finalizer guardian* idiom to defend against subclass forgetfulness to call finalize.  

### Methods Common to All Objects

### Classes and Interfaces
* Make each class or member as inaccessible as possible. Ensure objects references by public static final fields are immutable
* In public classes, use accessor methods, not public fields otherwise you can't enforce invariants, can't changing its representation as client code can be distributed far and wide
* Minimize Mutability: Make class final so it cannot be subclasses, make fields final and private, don't return reference to mutable fields (can utilize *defensive copying*). One trick to cache the results of an expensive computation in a nonfinal field in an immutable class. If absolutely necessary provide a companion mutable public class e.g. StringBuilder. Internally an immutable class could utilize a companion mutable class to provide common operations
* Favor composition (wrapper classes) over inheritance that way a subclass won't depend on superclass for its proper function
* Constructors must not invoke overridable methods, *clone* and *readObject* methods behave a lot like constructors so same applies to them. Three subclasses are sufficient to test an extendable class. Prohibit subclassing in classes that are not designed to be safely subclasses: declare it final or make all constructors private or package-private and provide static factory instead of constructor.
* Prefer interfaces over abstract class unless you prefer evolution (e.g. in a subsequent release all implementation of the abstract class will get a newly added concrete method) over flexibility. An interface is generally the best way to define a type that permits multiple implementations.
* Use interfaces only for defining types not to export constants.
* Prefer class hierarchy over tagged classes: If you're tempted to write a class with an explicit tag field, think about whether the tag could be eliminated and the class replaced by a hierarchy.
* When a concrete *function object* (strategy) is used only once, it is typically declared and instantiated as an anonymous class. When you require repeated use, it is generally implemented as a private static member class and exported in a public static final field who type is the strategy interface.
* 4 kinds of nested classes: *static member classes*, *nonstatic member classes* (holds reference to its enclosing instance which costs time and space, and can result in the enclosing instance being retained when it would otherwise be eligible for garbage collection), *anonymous classes*, and *local classes*. *Anonymous class*: they cannot have static members. *Local class*: declared anywhere a local variable can be declared and obeys same scoping rules and non-static local class will also hold reference to its enclosing instance and cannot contain static members. A public or protected static or non-static member class is an exported API element and cannot be changed from a non-static to a static class in a subsequent release without violating binary compatibility. If you declare a member class that does not require access to an enclosing instance, always put the *static* modifier in its declaration.


### Generics

### Enums and Annotations

### Methods

### General Programming

### Exceptions

### Concurrency

### Serialization
