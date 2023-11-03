
![](https://github.com/tabishfayyaz/notes-and-books/blob/master/images/main-thread-bad-idea.png)

- You can offload heavy work to background threads but there is a limit to how many background threads you can create so eventually you'll run out of memory:

![](https://github.com/tabishfayyaz/notes-and-books/blob/master/images/many-background-thread-bad-idea.png)

Using coroutines with memory consumption of one background thread you can perform so many heavy operations and that's what is cool about it:

![](https://github.com/tabishfayyaz/notes-and-books/blob/master/images/one-bg-thread-multiple-coroutines.png)

- Coroutines != Thread
- Coroutines are like light-weight threads
- Like threads, coroutines can run in parallel, wait for each other, and communicate with each other
- Creating coroutines is very cheap - almost free. Create thousands of them without any memory issues
