## Kotlin Programming: The Big Nerd Ranch Guide

by David Greenhalgh, Josh Skeen


Please [purchase the book](https://www.amazon.com/Kotlin-Programming-Nerd-Ranch-Guide-ebook/dp/B07FXQ7SQN) and support the authors.

### CHAPTER 1

The **Sandbox.iml** file contains configuration information specific to your single module. 

The **.idea** folder contains settings files for the entire project as well as those specific to your interaction with the project in the IDE


### CHAPTER 2

**static type checking:** type checking performed as code is entered or edited, and it tells you about programming mistakes before you even compile the program

String, Char, Boolean, Int, Double, List, Set, Map

If you ever have a question about the type of a variable, click on its name and press **Control-Shift-P**. IntelliJ will display its type

There are special cases where a **val** can return different values

A **compile-time constant** (const val MAX_EXPERIENCE: Int = 5000) must be defined outside of any function, including **main**, because its value must be assigned at compile time. A compile-time constant exists before any of these assignments take place. **main** and your other functions are called during runtime (when the program is executed), and the variables within them are assigned their values then. 

Basic types for a compile-time constant: String, Int, Double, Float, Long, Short, Byte, Char, Boolean

Press the Shift key twice to open the Search Everywhere dialog. Begin entering **show kotlin bytecode** in the search box

Unlike Java, Kotlin provides only one kind of type: reference types.
The Kotlin compiler will, where possible, use primitives in the Java bytecode, because they do indeed offer better performance

### CHAPTER 3

===	Evaluates whether the two instances point to the same reference.
!==	Evaluates whether the two instances do not point to the same reference.

1..5 includes 1, 2, 3, 4, and 5. Ranges can also be a sequence of characters.
You use the in keyword to check whether a value is within a range: if (healthPoints in 75..89)

the **_until_** function creates a range that excludes the upper bound of the range specified
**_downTo_** function creates a range that descends rather than ascends
a **_when_** expression behaves as though there were a == equality operator between the argument you provide in parentheses and the conditions you specify in the curly braces
Kotlin uses the **_Unit_** return type to signify exactly this: a function that returns no value.

**String templates:** `println("$name $healthStatus"), println("(Aura: $auraColor) (Blessed: ${if (isBlessed) "YES" else "NO"})")`

