## Effective Java

by Joshua Bloch

I own this book and made these notes for my own learning. Please [purchase the book](https://www.amazon.com/Effective-Java-2nd-Joshua-Bloch/dp/0321356683) and support the author.

### Creating and Destroying Objects
* Use static factory instead of constructors
* Consider a builder when faced with many constructor parameters
* Enforce singleton property with a private constructor or an enum type
* Enforce noninstantiability (e.g. UtilityClass) with a private constructor: raise exception inside constructor, in case it is accidentally called
* Avoid creating unnecessary object: boxing/unboxing inside a loop, new String("xyz") inside a loop. Immutable objects are great candidates for re-using
* Eliminate all obselete object references: returning object from a stack, hold weak reference to callbacks/listener, don't hold an object in a cache beyond use, *WeakHashMap*. An entry in a WeakHashMap will automatically be removed when its key (not the value) has no longer references outside of the WeakHashMap (cache)
* Avoid finalizers, *finally()* and *try-finally*: never do anything time-critical or depend on a finalizer to update critical persistent state. Require clients invoke explicit termination methods and as *safety-net* call the termination method inside try-finally and log a warning if resource has not been terminated. Invoke super class finalizer from **finally** block. Consider *finalizer guardian* idiom to defend against subclass forgetfulness to call finalize.  
