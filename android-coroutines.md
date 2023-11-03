
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
fun main () {   //Executes in main thread
    println("Main program starts: ${Thread.currentThread().name}")

    GlobalScope.launch {    //  creates a background coroutines that runs on a background thread
        println("Fake work starts: ${Thread.currentThread().name}")
        delay(1000) //  Coroutine is suspended but Thread (say T1) is free (not blocked)
        println("Fake work finished: ${Thread.currentThread().name}")   // Either T1 or some other thread
    }

    println("Main program ends: ${Thread.currentThread().name}")

    runBlocking { // Creates a coroutine that blocks the current main thread
        delay(2000) //application does not wait for all coroutines to exit process so we add a fake wait
    }
}
```

- `GlobalScope.launch()` is non-blocking in nature
- `runBlocking()` blocks the thread in which it operates

![](https://github.com/tabishfayyaz/notes-and-books/blob/master/images/delay-vs-sleep.png)

- A function with a `suspend` modifier is known as a suspending function
- The suspending functions are only allowed to be called from a coroutine or from another suspending function
- They cannot be called from outside a coroutine

  
