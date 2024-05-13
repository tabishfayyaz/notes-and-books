
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
- 3 most important coroutine builders/creators: `launch` & `GlobalScope.launch`, `async` & `GlobalScope.launch`, `runBlocking`
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
    - Default: default background thread, for complex and long-running calculations e.g. if you need to perform thousands of calculations that might block the main thread
    - Main: main thread also known as the UI thread
    - IO: IO thread, suitable for performing data-related operations, such as network requests, reading/writing from databases, or file I/O
    - Unconfined: runs the coroutine in the caller’s thread, inherit the context of the caller



## Reference
- https://www.youtube.com/watch?v=lmRzRKIsn1g
