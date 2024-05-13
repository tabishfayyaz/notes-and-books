
![](https://github.com/tabishfayyaz/notes-and-books/blob/master/images/main-thread-bad-idea.png)

- You can offload heavy work to background threads but there is a limit to how many background threads you can create so eventually you'll run out of memory:

![](https://github.com/tabishfayyaz/notes-and-books/blob/master/images/many-background-thread-bad-idea.png)

Using coroutines with memory consumption of one background thread you can perform so many heavy operations and that's what is cool about it:

![](https://github.com/tabishfayyaz/notes-and-books/blob/master/images/one-bg-thread-multiple-coroutines.png)

- Coroutines != Thread
- Coroutines are like light-weight threads
- Like threads, coroutines can run in parallel, wait for each other, and communicate with each other
- Creating coroutines is very cheap - almost free. Create thousands of them without any memory issues


```
fun main () {    // Executes in main thread
    println("Main program starts: ${Thread.currentThread().name}")

    thread {    //  creates a background thread (worker thread) using lambda expression
        println("Fake work starts: ${Thread.currentThread().name}")
        Thread.sleep(1000)  //  pretend doing some work ... may be file upload
        println("Fake work finished: ${Thread.currentThread().name}")
    }

    println("Main program ends: ${Thread.currentThread().name}")

    //application waits for all background threads to complete to exit the process
}
```

- Unlike threads, for coroutines, the application by default does not wait for it to complete to exit the process
- `Thread.sleep()` will block the thread and hence also block other coroutines in the thread so we use `delay` to suspend the coroutine
- The coroutine equivalent of the above:

```
fun main () {   // Executes in main thread
    println("Main program starts: ${Thread.currentThread().name}")

    GlobalScope.launch {    //  creates a background coroutines that runs on a background thread
        println("Fake work starts: ${Thread.currentThread().name}")
        delay(1000) //  Coroutine is suspended but Thread (say T1) is free (not blocked)
        println("Fake work finished: ${Thread.currentThread().name}")   // Either T1 or some other thread
    }

    runBlocking { // Creates a coroutine that blocks the current main thread
        delay(2000) //application does not wait for all coroutines to exit process so we add a fake wait
    }

    println("Main program ends: ${Thread.currentThread().name}")
}
```

The above code can also be written in a cleaner way as:

```
fun main () {   // Executes in main thread
    println("Main program starts: ${Thread.currentThread().name}")

    runBlocking { // Creates a coroutine that blocks the current main thread

        GlobalScope.launch {    // Thread: T1  
            println("Fake work starts: ${Thread.currentThread().name}")    // Thread: T1
            delay(1000)    // Coroutine is suspended but Thread (say T1) is free (not blocked)
            println("Fake work finished: ${Thread.currentThread().name}")   // Either T1 or some other thread
        }

        delay(2000) //application does not wait for all coroutines to exit process so we add a fake wait

        println("Main program ends: ${Thread.currentThread().name}")    // main thread
    }
}
```

or as a `runBlocking` function:
```
fun main() = runBlocking {    // Executes in main thread
    println("Main program starts: ${Thread.currentThread().name}")

    GlobalScope.launch {    //  creates a background coroutines that runs on a background thread
        println("Fake work starts: ${Thread.currentThread().name}")
        delay(1000) //  Coroutine is suspended but Thread (say T1) is free (not blocked)
        println("Fake work finished: ${Thread.currentThread().name}")   // Either T1 or some other thread
    }

    delay(2000) //application does not wait for all coroutines to exit the process so we add a fake wait

    println("Main program ends: ${Thread.currentThread().name}")
}
```

- `GlobalScope.launch()` is non-blocking in nature
- `runBlocking()` blocks the thread in which it operates

![](https://github.com/tabishfayyaz/notes-and-books/blob/master/images/delay-vs-sleep.png)

- A function with a `suspend` modifier is known as a suspending function
- The suspending functions are only allowed to be called from a coroutine or from another suspending function
- They cannot be called from outside a coroutine
- 3 most important coroutine builders/creators: `launch` & `GlobalScope.launch`, `async` & `GlobalScope.async`, `runBlocking`
- `GlobalScope.launch` (discouraged as if you lose reference to it'll run till the process ends) for things like file download, play music whereas local scope launch for things like some data computation, login operation

![](https://github.com/tabishfayyaz/notes-and-books/blob/master/images/global-scope-launch.png)

local `launch` builder (aka fire & forget because you don't block the calling code or wait for the result):
- launches a new coroutine without blocking the current thread
- will inherit coroutine scope and thread of immediate parent coroutine
- returns a reference to `Job` object which you can use for cancellation or wait for the coroutine to finish

```
fun main() = runBlocking {    // Executes in main thread
    println("Main program starts: ${Thread.currentThread().name}")  //  main thread

    val job : Job = launch {    //  local launch will inherit coroutine scope and thread of immediate parent coroutine
        println("Fake work starts: ${Thread.currentThread().name}")
        delay(1000) //  Coroutine is suspended but Thread: main is free (not blocked)
        println("Fake work finished: ${Thread.currentThread().name}")   // Either main thread or some other thread
    }

    job.join()    //waits for coroutine to finish
    println("Main program ends: ${Thread.currentThread().name}")    //  main thread
}
```

using `async` coroutine builder (non-blocking as well) you can return some data in addition to doing everything that you can do with `launch` and `Job`

```
fun main() = runBlocking {    // Executes in main thread
    println("Main program starts: ${Thread.currentThread().name}")  //  main thread

    val jobDeferred : Deferred<String> = async {    //  local launch will inherit coroutine scope and thread of immediate parent coroutine
        println("Fake work starts: ${Thread.currentThread().name}")
        delay(1000) //  Coroutine is suspended but Thread: main is free (not blocked)
        println("Fake work finished: ${Thread.currentThread().name}")   // Either main thread or some other thread
        "Hello Async"
    }

    val value : String = jobDeferred.await()
    //jobDeferred.join()
    println("Main program ends: ${Thread.currentThread().name}")    //  main thread
}
```

`runBlocking` is generally used to write test cases to test suspending functions

```
@Test
fun myFirstTest() = runBlocking {
    myOwnSuspendingFunc()
    Assert.assertEquals(10, 5+5)
}
```

To make a coroutine cancellable it has to be **cooperative:**
- Periodically invoke a suspending function that checks if a coroutine was canceled, only those suspending functions that belong to `kotlinx.coroutines` package will make the coroutine cooperative: `delay(), yield(), withContext(), withTimeout()` etc.
- explicitly check for the cancellation status within the coroutine: `CoroutineScope.isActive` boolean flag (`true` when coroutine is active, `false` when coroutine is cancelled)

```
fun main() = runBlocking {    //Executes in main thread
    println("Main program starts: ${Thread.currentThread().name}")  //  main thread

    val job : Job = launch {
        for (i in 0..500){
            print("$i.")
            delay(50)    // or yield() or any other suspending function as per your need
        }
    }

    delay(200)  //let's print few values before we cancel
    // job.cancel()
    // job.join()  //cancel but waits for coroutine to finish
    job.cancelAndJoin()

    println("\nMain program ends: ${Thread.currentThread().name}")    //  main thread
}
```

- If you want to execute a suspending function from a `finally` block then wrap the code within `withContext(NonCancellable)` function 
- Handle `CancellationException`:

```
fun main() = runBlocking {    //    Executes in main thread
    println("Main program starts: ${Thread.currentThread().name}")  //  main thread

    val job : Job = launch (Dispatchers.Default) {
        try {
            for (i in 0..500){
                print("$i.")
                delay(5)    //    or yield() or any other suspending function as per your need
            }
        } catch (ex: CancellationException) {   //    a suspending function will throw cancellable exception
            println("Exception caught safely")
        } finally {
            println("\nClose resources in finally")
        }

    }

    delay(10)  //    let's print few values before we cancel
    job.cancelAndJoin()

    println("\nMain program ends: ${Thread.currentThread().name}")    //  main thread
}
```

- `withTimeout` and `withTimeoutOrNull` and also coroutine builders:

```
withTimeout(2000) {
        try {
            for (i in 0..500){
                print("$i.")
                delay(500)
            }
        } catch (ex: TimeoutCancellationException) {
            //  ..code
        } finally {
            //  ..code
        }
    }
```

```
val result: String? = withTimeoutOrNull(2000) {
    for (i in 0..500) {
        print("$i.")
        delay(500)
    }
    "I am done"
}
```

- Code execution within coroutine is by default sequential, all methods execute in sequence:

```
fun main() = runBlocking {    //Executes in main thread
    println("Main program starts: ${Thread.currentThread().name}")  //  main thread
    
    val time = measureTimeMillis {
        val msgOne = getMessageOne()
        val msgTwo = getMessageTwo()
        println("The entire message is: ${msgOne + msgTwo}")
    }

    println("Completed in $time ms")
    println("\nMain program ends: ${Thread.currentThread().name}")    //  main thread
}

suspend fun getMessageOne(): String {
    delay(1000L)    //  pretend to do some work
    return "Hello "
}

suspend fun getMessageTwo(): String {
    delay(1000L)    // pretend to do some work
    return "World"
}
```

- To get concurrent execution you can use `async` or `launch` as child coroutine builders:

```
fun main() = runBlocking {    //Executes in main thread
    println("Main program starts: ${Thread.currentThread().name}")  //  main thread

    val time = measureTimeMillis {
        val msgOne : Deferred<String> = async { getMessageOne() }   //launch can also be used for concurrency
        val msgTwo : Deferred<String> = async { getMessageTwo() }
        println("The entire message is: ${msgOne.await() + msgTwo.await()}")
    }

    println("Completed in $time ms")
    println("\nMain program ends: ${Thread.currentThread().name}")    //  main thread
}

suspend fun getMessageOne(): String {
    delay(1000L)    //  pretend to do some work
    return "Hello "
}

suspend fun getMessageTwo(): String {
    delay(1000L)    // pretend to do some work
    return "World"
}
```

- Only execute the coroutine if the result is used in the code:

```
fun main() = runBlocking {    //Executes in main thread
    println("Main program starts: ${Thread.currentThread().name}")  //  main thread

    val time = measureTimeMillis {
        val msgOne : Deferred<String> = async (start = CoroutineStart.LAZY) { getMessageOne() }
        val msgTwo : Deferred<String> = async (start = CoroutineStart.LAZY) { getMessageTwo() }
        //    println("The entire message is: ${msgOne.await() + msgTwo.await()}")  // uncomment this line to see lazy coroutine execution
    }

    println("Completed in $time ms")
    println("\nMain program ends: ${Thread.currentThread().name}")    //  main thread
}

suspend fun getMessageOne(): String {
    delay(1000L)    //  pretend to do some work
    println("After working in getMessageOne()")
    return "Hello "
}

suspend fun getMessageTwo(): String {
    delay(1000L)    // pretend to do some work
    println("After working in getMessageTwo()")
    return "World!"
}
```

- Each coroutine has its own `CoroutineScope` instance attached to it
- Each coroutine irrespective of parent or child has its own scope
- `CoroutineScope` represents what kind of coroutine you have created

```
fun main() = runBlocking {
    println("runBlocking: $this")

    launch {
        println("launch: $this")

        launch {
            println("child launch: $this")  //different object hash in log
        }
    }

    val job = async {
        println("async: $this")
    }
}
```

- Similar to `CoroutineScope` each coroutine has its own `CoroutineContext` (dispatcher, job)
    - dispatcher determines the thread of a coroutine
- `CoroutineContext` can be inherited from parent coroutine to child coroutine (read comments):

```
fun main() = runBlocking {  // Thread: main
    //  this: CoroutineScope instance
    //  coroutineContext: CoroutineContext instance

    /*
        Without Parameter: CONFINED     [CONFINED DISPATCHER]
        - Inherits CoroutineContext from immediate parent coroutine
        - Even after delay() or suspending function, it continues to run in the same thread
     */
    launch {
        println("C1: ${Thread.currentThread().name}")   // Thread: main
        delay(1000)
        println("C1 after delay: ${Thread.currentThread().name}")   // Thread: main
    }

    /*
        With parameter: Dispatchers.Default [similar to GlobalScope.launch{}]
        - Gets its own context at Global level. Executes in a separate background thread.
        - After delay() or suspending function execution, it continues to run either in the same thread or some other thread
     */
    launch (Dispatchers.Default) {
        println("C2: ${Thread.currentThread().name}")   // Thread: T1
        delay(1000)
        println("C2 after delay: ${Thread.currentThread().name}")   // Thread: T1 or some other thread
    }

    /*
        With parameter: Dispatchers.Unconfined      [UNCONFINED DISPATCHER]
        - Inherits CoroutineContext from the immediate parent coroutine
        - After delay() or suspending function execution, it continues to run in some other thread
     */
    launch (Dispatchers.Unconfined) {
        println("C3: ${Thread.currentThread().name}")   // Thread: main
        delay(100)
        println("C3 after delay: ${Thread.currentThread().name}")   //Thread: some other thread T1

        //now this is coroutineContext of parent
        launch (coroutineContext) {
            println("C5: ${Thread.currentThread().name}")   // Thread: T1
            delay(1000)
            println("C5 after delay: ${Thread.currentThread().name}")   // Thread: T1
        }
    }

    // Using coroutineContext property to flow context from parent to child
    launch (coroutineContext) {
        println("C4: ${Thread.currentThread().name}")   // Thread: main
        delay(1000)
        println("C4 after delay: ${Thread.currentThread().name}")   // Thread: main
    }

    println("...Main Program...")
}
```

## Additional Notes

- Co-Routines: Cooperating Functions: It can execute a few lines of functionA and then execute a few lines of functionB and then again a few lines of functionA and so on. This will be helpful when a thread is sitting idle, in which case it can execute a few lines of another function.
- Threads are managed by the OS and coroutines by the users. Coroutines do not replace threads, it's more like a framework to manage them.
- Coroutines are lightweight threads: A lightweight thread means it doesn't map on the native thread, so it doesn't require context switching on the processor, so they are faster.
- One can think of a coroutine as a light-weight thread. Like threads, coroutines can run in parallel, wait for each other and communicate. The biggest difference is that coroutines are very cheap, almost free: we can create thousands of them, and pay very little in terms of performance. True threads, on the other hand, are expensive to start and keep around. A thousand threads can be a serious challenge for a modern machine.
- There are four types of Dispatchers:
    - Default: default background thread, for complex and long-running calculations e.g. if you need to perform thousands of calculations that might block the main thread, matrix multiplication, bitmap operations, 
    - Main: main thread also known as the UI thread
    - IO: IO thread, suitable for performing network/data-related operations, such as network requests, reading/writing from databases, or file I/O
    - Unconfined: runs the coroutine in the caller’s thread, inherit the context of the caller

## QA

### What is difference between suspend and blocking

In Kotlin coroutines, the concepts of suspension and blocking are important to understand as they affect the behavior of asynchronous code execution:

1. **Suspending Functions**:
   - **Suspend Functions**: These are functions that can be paused (suspended) and resumed later without blocking the thread. They are marked with the `suspend` keyword in Kotlin.
   - **Non-blocking**: When a coroutine encounters a suspend function, it can pause its execution without blocking the thread it's running on. This allows other coroutines to run concurrently on the same thread, making more efficient use of resources.
   - **Asynchronous Operations**: Suspending functions are commonly used to perform asynchronous operations such as network requests or database queries. They allow the coroutine to suspend its execution while waiting for the operation to complete, without blocking the thread.

2. **Blocking Functions**:
   - **Blocking Functions**: These are functions that block the thread they are called on until they complete their operation. They typically perform synchronous operations that may take some time to complete.
   - **Thread Blocking**: When a coroutine calls a blocking function, it blocks the thread it's running on until the function returns. This means that no other coroutines can execute on the same thread while the blocking function is running.
   - **Avoiding in Coroutines**: Using blocking functions in coroutines should be avoided whenever possible, especially on the main/UI thread, as it can lead to poor performance and unresponsive user interfaces.

In summary, suspend functions allow coroutines to suspend their execution without blocking the thread, enabling efficient asynchronous programming, while blocking functions halt the execution of coroutines and potentially block the thread, which can lead to performance issues in asynchronous code.

### is `launch` a blocking function?

No, `launch` is not a blocking function in Kotlin coroutines. It is a coroutine builder function that starts a new coroutine without blocking the current thread.

When you call `launch { ... }`, it creates a new coroutine and starts executing the code inside the lambda expression concurrently with the caller coroutine or thread. The `launch` function returns a `Job` object immediately, allowing the caller to continue execution without waiting for the launched coroutine to complete.

The key points about `launch` are:

1. It is non-blocking and does not suspend the current thread or coroutine.
2. It starts a new coroutine in the specified CoroutineScope and CoroutineContext.
3. The launched coroutine runs concurrently with the caller.
4. The caller can optionally await or join the launched coroutine using the returned Job object.

In contrast, functions like `runBlocking` are blocking because they start a new coroutine but also block the current thread until that coroutine completes. `runBlocking` is typically used in main functions or tests to bridge regular blocking code with suspending code.

So in summary, `launch` is a non-blocking coroutine builder that allows you to start new coroutines without blocking the current thread or suspending the caller coroutine, enabling lightweight concurrency and asynchronous programming.

### is `await` a blocking function?

No, `await` is not a blocking function in Kotlin coroutines. It is a suspending function that suspends the coroutine's execution until the awaited value is available, without blocking the underlying thread.

Here are the key points about `await`:

1. `await` is an extension function on the `Deferred<T>` interface, which is returned by the `async { ... }` coroutine builder.

2. When you call `await()` on a `Deferred<T>` object, it suspends the current coroutine until the asynchronous operation represented by the `Deferred` completes and its result becomes available.

3. While the coroutine is suspended at the `await()` call, the underlying thread is not blocked. Instead, it is released and can be used by other coroutines or operations.

4. Once the awaited value is ready, the suspended coroutine is resumed transparently with the awaited value, without the need for callbacks or manual thread management.

5. `await` is a suspending function, which means it can only be called from another coroutine or suspending function, not from regular functions or blocking code.

In contrast, blocking functions like `Thread.sleep()` or `runBlocking { ... }` actually block the underlying thread, preventing it from doing any other work until the operation completes. This can lead to inefficient resource utilization and potential performance issues, especially in scenarios involving I/O operations or delays.

By using `await` and other suspending functions, Kotlin coroutines enable efficient utilization of threads, improved responsiveness, and better scalability compared to traditional blocking approaches. The non-blocking nature of `await` is a key aspect of coroutines' lightweight concurrency model.

### What is the difference between `launch` and `async`

The main difference between `launch` and `async` in Kotlin coroutines lies in how they handle the result of the coroutine and their return types:

1. `launch` returns a `Job` object and does not carry any resulting value from the coroutine. It is used for fire-and-forget style coroutines where you don't need to get the result back. For example, updating a database or performing a side-effect operation.

2. `async` returns a `Deferred<T>` object, which represents the future result `T` of the coroutine. The `Deferred` object has an `await()` function that returns the result when it becomes available. This is useful when you need to get the result of the coroutine for further processing.

Another key difference is how they handle exceptions:

3. If an exception occurs inside the `launch` block, it crashes the application if not handled properly.

4. If an exception occurs inside the `async` block, it is stored inside the resulting `Deferred` object and is not propagated further unless you explicitly handle it using `await()`.

To summarize:

- Use `launch` when you want to start a coroutine for its side-effects and don't need to get the result back.
- Use `async` when you need to perform an asynchronous operation and get the result to use in further processing.
- `async` provides structured concurrency and exception handling, while `launch` requires explicit handling of exceptions.

Both `launch` and `async` are non-blocking and start new coroutines concurrently, but `async` allows you to retrieve the result and handle exceptions more explicitly.

### What is the difference between `coroutineScope` vs `supervisorScope`

1. **`coroutineScope`**:
   - The `coroutineScope` function is a **suspending function** that creates a new coroutine scope.
   - **Behavior**:
     - It **waits** for all its child coroutines to complete.
     - If any child coroutine fails (throws an exception), it **cancels** all other children immediately.
     - Essentially, it follows a **fail-fast** approach: any failure propagates up the hierarchy, leading to cancellation of the entire scope.
   - **Use Case**:
     - Use `coroutineScope` when you want to **cancel the entire scope** if any child coroutine fails.
     - For example, when you need to perform multiple network requests and want to ensure that all of them succeed or fail together.

2. **`supervisorScope`**:
   - The `supervisorScope` function also creates a new coroutine scope.
   - **Behavior**:
     - It **isolates** failures within its scope.
     - If any child coroutine fails, it **does not cancel** other children.
     - The supervisor scope continues executing other child coroutines even if one of them throws an exception.
   - **Use Case**:
     - Use `supervisorScope` when you want to **continue with other tasks** even if some child coroutines fail.
     - For example, when handling multiple independent tasks (e.g., downloading images) and you don't want one failure to affect the others.

3. **Example**:
   - Suppose you have two network requests (async coroutines) within a scope:
     ```kotlin
     suspend fun main() {
         println(compute())
     }
     
     suspend fun compute(): String = supervisorScope {
         val color = async { delay(60_000); "purple" }
         val height = async<Double> { delay(100); throw HttpException() }
         "The box is %.1f inches tall and it's %s".format(height.await(), color.await())
     }
     ```
   - In this example, even though `height.await()` throws an exception, the `color.await()` still completes successfully because we're using `supervisorScope`.

Remember to choose the appropriate scope based on your use case: whether you want to cancel everything on failure (`coroutineScope`) or continue with other tasks (`supervisorScope`)!

### How do you convert a callback to coroutine

### What is Coroutine Context

### What is coroutine scope, context and job

### What is suspendCoroutine and suspendCancellableCoroutine

### What is lifecycleScope, viewModelScope and GlobalScope

### How to make a network call using Kotlin coroutines

### How to make network calls in series using Kotlin coroutines

### How to make network calls in parallel using Kotlin coroutines

### How to fetch or insert entity in database using Kotlin Coroutines

### How to run a long running task using Kotlin Coroutines

### How to run two long running tasks in parallel using Kotlin Coroutines

### How to add timeout to a task using Kotlin Coroutines

### How to handle error in Kotlin Coroutines using Try-Catch

### How to handle error in Kotlin Coroutines using CoroutineExceptionHandler

### How to use supervisorScope to ignore error of a task and continue with other task.

### How write unit-test for ViewModel which uses Kotlin Coroutines and LiveData



## Reference
- https://www.youtube.com/watch?v=lmRzRKIsn1g
