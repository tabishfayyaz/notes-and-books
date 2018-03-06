## Writing Solid Code

by Steve Maguire

I own this book and made these notes for my own learning. Please [purchase the book](https://www.amazon.com/Writing-Solid-Code-20th-Anniversary/dp/1570740550) and support the author.

*All of the techniques and guidelines presented in this book are the result of programmers asking themselves two questions over & over again, year after year, for every bug found in their code:*

*1. How could I have automatically detected this bug?*
*2. How could I have prevented this bug?*

### 1. A Hypothetical Compiler
* The best way to eliminate bugs in your code is to find them as early as possible. Look for ways to catch bugs automatically, with minimal effort.
* Strive to reduce the amount of programmer skill necessary to catch bugs. Optional compiler or lint warnings don't require any programmer skills to catch bugs.

### 2. Assert Yourself
* Assertions are a shorthand way to write debugging checks. Use them to catch illegal conditions that should never arise. Don't confuse such conditions with error conditions, which you must handle in the final product.
* Maintain shipping and debugging versions of your program. Shrink-wrap the shipping version, but use the debugging version as much as possible to catch bugs quickly.
* Textbooks encourage programmers to program defensively, but remember that this coding style hides bugs. When you write defensive code, use assertions to alert you if the "can't happen" cases do happen.
* Use assertions to validate function arguments and to alert programmers when they do something undefined. The more rigidly you define your functions, the easier it will be to validate the arguments.
* Once you've written a function, review it and ask yourself, "What am I assuming?" If you find an assumption, either assert that your assumption is always valid, or rewrite the code to remove the assumption. Also ask, "What is most likely to be wrong in this code, and how an I automatically detect the problem?" Strive to implement tests that catch bugs at the earliest possible moment.

### 3. Fortify Your Subsystems
* Look at your subsystems and ask yourself how programmers are likely to misuse them. Add assertions and validation checks to catch hard-to-spot and common bugs
* You can't fix bugs if you can't repeatedly find them. Look for anything that can cause random behaviour and remove it from the debug version of your program. Setting the "undefined" memory to a constant garbage value is one example of removing random behaviour. That way, if something references the memory while it's still undefined, you'll get the same results every time you execute the offending code.
* If your subsystem release memory (or other resources) and creates "garbage", scramble the data so that it really looks like garbage; otherwise, code somewhere may continue to use the data without your noticing it.
* Make sure your tests work even for programmers who are unaware of them. The best tests are those that require no knowledge of their existence.
* If possible, build tests into your subsystems instead of on top of them. And don't wait until the subsystem is coded to look for ways to validate it. For each design you consider, ask yourself, "How can I thoroughly validate this implementation?" If you find that it would be difficult or impossible to test the implementation, seriously consider a different design, even if that means trading size or speed for the ability to test the system.
* Similarly, if your subsystems contain behavior that may happen, add debug code to make sure such behaviour does happen. Making everything happen increases your odds of catching bugs in code that is normally not executed.
* Think twice before throwing out a validation test because it would be too slow or use too much memory. Remember the validation code won't appear in the ship version of your program. If you find yourself thinking, "This test would be too slow (or too big)," stop and ask yourself, "How can I keep this test, but make it faster (or smaller)?"

### 4. Step Through Your Code
* Bugs don't grow in code spontaneously; they are the result of a programmer's writing new code or changing existing code. If you want to find bugs in your code, there is no better method than stepping through every line of the code the moment it's compiled.
* Although your gut reaction might be that walking through your code will take a lot of time, you gut reaction would be wrong. Yes, initially it will take more time - until walking through your code becomes habitual. Once that happens, you'll zip right through your code.
* Be sure to step through every code path - especially in your error handling code - at least once. Don't forget that the &&, ||, and ?: operators have two code paths to test.
* In some cases, you may need to step through code at the assembly language level. While you don't need to do this often, don't avoid doing it when its necessary.

*Never allow the same bug to bite you twice*

### 5. Candy - Machine Interfaces
* Create function interfaces that are easy to use and understand: Ensure that input and output represent exactly one type of data. Mixing error and other special-purpose values into your inputs and outputs does nothing but clutter your interfaces.
* Design your function interfaces in such a way that programmers are forced to think about all the important details, such as handling error conditions. Don't make it easy for them to ignore or forget the details.
* Consider how programmers must call your functions. Look for flaws in your function interfaces that can cause programmers to unwittingly introduce bugs. Of particular importance: Strive to write functions that always succeed so that callers don't need to do any error handling.
* Break apart multipurpose functions. Not only do the more specific function names increase comprehension (for example, ShrinkMemory instead of realloc), but you can use more rigid assertions to automatically detect bad arguments.
* Document your interfaces to show programmers how to properly call your functions. Emphasize the danger zones.
* Increase comprehension and thus reduce bugs by making sure that the calls to your function are understandable to programmers who have to read those calls. Magic numbers and boolean arguments work against this goal.

### 6. Risky Business
* Choose your data types carefully. Even though the ANSI standard requires all implementations to support chars, ints, longs, and so on, it does not concretely define those types. Protect yourself from bugs by relying only on what the ANSI standard specifically guarantees.
* Remember that it's possible for your algorithm to be correct but still have bugs because of less-than-ideal characteristics of the hardware it runs on. In particular, always check that your calculations and tests don't overflow or underflow your data types.
* Every function should have one well-defined task, but more that that, it should have only one way to accomplish that task. If the same code executes regardless of the inputs, you decrease the odds of having undetected bugs.
* Be faithful to your design. The easiest way to introduce subtle bugs is to cheat on the implementation.
* When you write expressions, try not to mix different types of operators. If you must mix operators, use parentheses to separate the operations.
* An if statement is a particularly good warning sign that you may be doing more work than necessary. Strive to eliminate every unnecessary if statement in your code by asking yourself, "How can I change my design to remove this special case?" Sometimes you may have to alter your data structures, and at other times you may have to alter the way you view them.
* The special case of special cases is error handling. If possible, avoid calling functions that can fail. But if you must call a function that can return an error, try to localize the error handling this will increase your chances of finding bugs in the error handling code.
* In some cases, it's possible to eliminate general error handling by guaranteeing that what you want to do can't fail. That may mean handling the error once during initialization, or it may mean changing your design.

### 7. Treacheries of the Trade
* If you're working with data you don't own, don't write to it, even temporarily. And though you might think that reading from data is always safe, remember that reading from memory mapped I/O may be hazardous to your hardware
* Don't reference memory once you have released it. There are two many ways in which referencing free memory can cause bugs.
* Don't write functions that rely on specific implementations of other functions.
* Don't write code the way lawyers write contracts. If an average programmer can't read and understand your code, it's too complicated; use simpler language.
* It may be tempting, for efficiency, to pass data in global or static buffers, but that's fraught with dangers. If you write a function that creates data useful only to the caller, return the data to the caller, or guarantee that you won't unexpectedly change that data.
* Logically, it would seem that the efficient expression of a concept in C would result in similarly efficient machine code. It doesn't work that way. Before you take a clean multiline piece of C code and squash it into something that fits on one line, be sure that you're getting better machine code for your trouble. Even then, remember that local efficiency gains are rarely noticeable and are not usually worth mucking up your code.


### 8. The Rest is Attitude
* Bugs don't create themselves; nor do they fix themselves. If you have a bug report but you can't reproduce the bug, don't assume that the tester was seeing things. Make the effort to find the bug, even if that means reverting to an older version of the program.
* Don't fix bugs "later". It's becoming alarmingly common to read of major products being canceled because of runaway bug lists. Your project won't suffer that fate if you fix bugs as you find them. You can't have a runaway bug list if the project is always near zero bugs.
* When you track down a bug, always ask yourself whether the bug is a symptom of a larger bug. Yes, it's easier to fix the symptom you've tracked down, but you should always make the effort to find the true cause.
* Don't write unnecessary code or make unnecessary fixes. Let your competitors implement cool but worthless features, clean up code, and slip their ship dates because of "free" features that aren't. Let your competition waste time fixing the unnecessary bugs that come with all that useless code.
* Remember that being flexible is not the same as being easy to use. When you design functions and features, focus on making them easy to use; if they're merely flexible - as the realloc function - you're not making them more useful; you're making it more difficult to find the bugs.
* Resist the urge to "try" things to achieve a desired effect. Use the time you would have spent trying things to do some research to find the correct solution. If you have to, call the company responsible for your operating system and talk to their developer support group. That's better than coming up with oddball implementations that may break in the future.
* Write your code in chunks small enough to thoroughly test, and don't skimp on your testing. Remember, if you don't test your code, it's possible that nobody will. Whatever you do, don't expect the testing group to test your code for you.
* Determine what the priorities are for your group and follow them: Correctness, Global Efficiency, Testability, Size, Local efficiency, Maintainability, Consistency, Personal Convenience, Personal Expression?
