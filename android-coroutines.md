
![bad idea on main thread](https://github.com/tabishfayyaz/notes-and-books/blob/master/images/main-thread-bad-idea.png)

- You can offload heavy work to background threads but there is a limit to how many background threads you can create so eventually you'll run out of memory

![](https://github.com/tabishfayyaz/notes-and-books/blob/master/images/many-background-thread-bad-idea.png)

Using coroutines with memory consumption of one background thread you can perform so many background operations and that's what is cool about it

