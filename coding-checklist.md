*Source: Writing Solid Code by Steve Maguire*

## Summary

**Design**
- **Risk Consideration**: Evaluate risks in implementation, maintenance, and usage.
- **Behavior Analysis**: Avoid undefined, meaningless, or rare behaviors.
- **Flexibility and Assumptions**: Eliminate unnecessary flexibility and assumptions.
- **Data Handling**: Avoid static or global buffers, ensure functions are single-tasked.
- **Special Cases**: Isolate special case handling.
- **Inputs and Outputs**: Ensure each represents a single type of data.
- **Function Calls**: Make functions intuitive and prevent common misuse.
- **Error Handling**: Redefine functions to minimize error conditions.
- **Testing Feasibility**: Choose designs that can be validated through unit tests.

**Implementation**
- **Design Consistency**: Ensure implementation matches the design.
- **Assumption Minimization**: Avoid nonportable data types and arbitrary implementations.
- **Expression Safety**: Prevent overflows, underflows, and risky expressions.
- **Code Readability**: Use understandable, mainstream C over arcane constructs.
- **Task Simplification**: Avoid special-case code; aim for single code paths.
- **Error Handling**: Eliminate calls to functions that return errors where possible.
- **Memory Safety**: Avoid referencing released memory and respect memory allocation limits.

**Adding Debug Support**
- **Assertions**: Validate function arguments and assumptions, detect illegal behavior.
- **Defensive Programming**: Use assertions to find bugs instead of fixing them invisibly.
- **Clarity**: Ensure assertions are clear and commented.
- **Debug Code**: Set and destroy memory contents for easier bug detection.
- **Algorithm Verification**: Use secondary debug algorithms for critical checks.
- **Startup Checks**: Perform early bug detection, especially for data tables.

**Testing**
- **Compiler Warnings**: Ensure code compiles without warnings and passes lint tests.
- **Debugger Use**: Step through new code, focusing on data flow.
- **Code Cleanup**: Treat cleaned-up code as new and thoroughly test it.
- **Unit Tests**: Write unit tests for new code.

**Debugging**
- **Bug Tracking**: Confirm the bug's presence in the reported version.
- **Root Cause**: Identify the true cause of the bug, not just its symptoms.
- **Prevention Guidelines**: Develop guidelines to prevent future occurrences.
- **Automatic Detection**: Use assertions, debug code, and process changes to catch similar bugs.

By adhering to these guidelines, you can ensure robust, maintainable, and error-resistant code.

## Coding Checklist

To make effective use of these checklists, review them each time you add new code to your project. From a practical standpoint, “each time” really means the “next few times” you write new code. After that, you should have developed a sixth sense for code that bends or breaks the guidelines.

### DESIGN
When you consider different designs for a feature, don’t stop with the design that gives you the fastest or the smallest result. Consider the risks involved in implementing, maintaining, and using the code that will result from your design. For each possible design, review these points.
- Does this design include undefined or meaningless behavior? What about random or rare behavior? Does the design allow unnecessary flexibility or make unnecessary assumptions? Are there arbitrary details in the design?
- Do you pass any data in static or global buffers? Do any functions rely on the internal workings of other functions? Do any functions do more that one task?
- Does your design have to handle any special cases? Have you isolated the code that handles those special cases?
- Look at the inputs and outputs of your functions. Does each of the inputs and outputs represent exactly one type of data, or do some of them contain error values or other hard-to-notice values? Robust interfaces make every input and output explicit so that programmers can’t miss important details such as the NULL error value returned by malloc, or the fact that realloc can release a memory block if you pass in a size of 0.
- Anticipate how programmers will call your functions. Does the “obvious” approach work correctly? Recall that in realloc’s case, the obvious approach creates lost memory blocks.
- On the maintenance side, are your functions readable at the point of call? Each function should perform one task, and its arguments should make the meaning of the call clear. The presence of TRUE and FALSE arguments often indicates that a function is doing more than one task, or that it is not well designed.
- Do any of your functions return error values? Is it possible to redefine those functions to eliminate the error conditions? Remember that when a function returns an error, the error must be handled – or mishandled – at every point of call.
- Most important, is it possible to automatically and thoroughly validate the design using a unit test? If not, you should consider using an alternative design that can be tested.

### IMPLEMENTATION
After implementing your design, you should review these points to ensure that your implementation is robust and error resistant.
- Compare your implementation to your design. Have you accurately implemented the design? Be careful. Minor differences between your design and your implementation can trip you up. Remember the UnsToStr example that broke because it used non-negative integers when the design called for integers that were unsigned.
- Do you make unnecessary assumptions in the code? Have you used nonportable data types when portable data types would work? Are there any arbitrary aspects of the implementation?
- Examine the expressions in your code. Can any of them overflow or underflow? What about your variables?
- Have you used nested ?: operators or other risky C language idioms such as shifting to divide? Have you mixed bitwise operators and arithmetic operators without good cause? Have you used any C idioms in a questionable way? For example, using the 0/1 result of a logical expression in an arithmetic context? Rewrite risky expressions using comparable yet safer expressions.
- Take a close look at your code. Have you used any arcane C that the average programmer on your team wouldn’t understand? Consider rewriting the code using mainstream C.
- Each of your functions probably does a single task, but is that task implemented using a single code path, or is the task actually achieved using different code to implement various special cases? If the task is implemented using special-case code, can you eliminate those special cases by using an alternative algorithm? Try to eliminate every if statement in your code.
- Do you call any functions that return errors? Can you alter your design so that the call is unnecessary and thus eliminate the need to do error handling?
- Do you reference memory you have no right to touch? Specifically, do you reference memory you have released? Do you peek at private data structures owned by other subsystems?
- If your functions take pointers to inputs or outputs, does your code restrict its references to only the memory required to hold those inputs and outputs? If not, your code may be making an erroneous assumption about how much memory the caller has allocated for that data.
 
### ADDING DEBUG SUPPORT
Adding assertions and other debugging code to your implementations can reduce the time required to find any bugs hiding in your code. This checklist points out worthwhile assertions and debugging code you should consider using.
- Have you used assertions to validate your function arguments? If you find that you can’t validate a particular argument because you don’t have enough information, would maintaining extra debug information help? Recall how the debug-only sizeofBlock function was useful in validating pointers to allocated memory.
- Have you used assertions to validate your assumptions, or to detect illegal uses of undefined behavior? Asserting for undefined behavior prevents programmers from abusing unspecified details of your implementations.
- Defensive programming “fixes” internal bugs when they occur, making such bugs hard to spot. Have you used assertions to detect these bugs in the DEBUG version of your program? (Ofcourse, this view of defensive programming doesn’t apply to defensive programming used to correct bad end-user inputs.)
- Are your assertions clear? If not, be sure to include comments to explain the tests. Unfortunately, when programmers get an assertion failure and don’t understand the purpose of the test, they will often assume that the assertion is invalid and remove it. Comments help preserve your assertions.
- If your code allocates memory, have you used debug-only code to set the uninitialized contents to a known but obviously garbage state? Setting memory to a consistent value will make it easier to find and reliably reproduce bugs that use uninitialized memory.
- If your code releases memory, does it first destroy the contents so that you don’t have valid-looking garbage hanging around?
- Are any of your algorithms critical enough that you should use a second, but different, debug-only algorithm to verify the primary one?
- Are there any debug checks you can make at program startup to detect bugs at the earliest possible moment? In particular, are there any data tables you could validate at program startup?

### TESTING
It is vitally important that programmers test their code, even if it means slipping the schedule. The questions in this section point out the most beneficial testing steps to take.
- Does the code compile without generating any warnings, including all optional compiler warnings? If you’re using lint, or a similar diagnostic tool, does the code pass all tests? Does the code pass your unit tests? If you’ve skipped any of these steps, you’re missing an opportunity to easily detect bugs.
- Have you stepped through all new code using a debugger, focusing not only on the code, but also on the data flowing through that code? This is perhaps the best approach to catching bugs in your implementations.
- Have you "cleaned up” any code? If so, have you tested the code? Have you stepped through the code in a debugger? Remember, code that has been cleaned up is actually new code that must be thoroughly tested.
- Should you write a unit test for the new code?

### DEBUGGING
You should review the questions below each time you have to track down a reported bug.
- Were you able to find the reported bug? If not, remember that bugs don’t just go away; either they’re hiding, or they have been fixed already. To determine which is true, you should look for the bug in the same version of the code in which the bug was reported.
- Have you found the true cause of the bug or merely a symptom of the bug? Be sure to track down the cause of the bug.
- How could this bug have been prevented? Come up with a precise guideline that could prevent this bug in the future.
- How could this bug have been detected automatically? Would as assertion catch it? What about some DEBUG code? What changes in your coding practices or process would help?
