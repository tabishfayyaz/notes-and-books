## Kotlin Programming: The Big Nerd Ranch Guide

by David Greenhalgh, Josh Skeen

Please [purchase the book](https://www.amazon.com/Kotlin-Programming-Nerd-Ranch-Guide-ebook/dp/B07FXQ7SQN) and support the authors.

### CHAPTER 1

The **Sandbox.iml** file contains configuration information specific to your single module. 

The **.idea** folder contains settings files for the entire project as well as those specific to your interaction with the project in the IDE


## Variables, Constants & Types

**static type checking:** type checking performed as code is entered or edited, and it tells you about programming mistakes before you even compile the program

_String, Char, Boolean, Int, Double, List, Set, Map_

If you ever have a question about the type of a variable, click on its name and press **Control-Shift-P**. IntelliJ will display its type

There are special cases where a **val** can return different values

A **compile-time constant** (const val MAX_EXPERIENCE: Int = 5000) must be defined outside of any function, including **main**, because its value must be assigned at compile time. A compile-time constant exists before any of these assignments take place. **main** and your other functions are called during runtime (when the program is executed), and the variables within them are assigned their values then. 

Basic types for a compile-time constant: _String, Int, Double, Float, Long, Short, Byte, Char, Boolean_

Press the Shift key twice to open the Search Everywhere dialog. Begin entering **show kotlin bytecode** in the search box

Unlike Java, Kotlin provides only one kind of type: reference types.
The Kotlin compiler will, where possible, use primitives in the Java bytecode, because they do indeed offer better performance

## Conditionals

`===`	Evaluates whether the two instances point to the same reference.
`!==`	Evaluates whether the two instances do not point to the same reference.

`1..5` includes 1, 2, 3, 4, and 5. Ranges can also be a sequence of characters.
You use the in keyword to check whether a value is within a range: if (healthPoints in 75..89)

the **_until_** function creates a range that excludes the upper bound of the range specified
**_downTo_** function creates a range that descends rather than ascends
a **_when_** expression behaves as though there were a == equality operator between the argument you provide in parentheses and the conditions you specify in the curly braces

**String templates:** `println("$name $healthStatus"), println("(Aura: $auraColor) (Blessed: ${if (isBlessed) "YES" else "NO"})")`

## Functions

**single-expression-functions** - you can omit the return type, curly braces and return statement:
`private fun foo(number: Int = 2) = println("")`

Kotlin uses the **_Unit_** return type to signify exactly this: a function that returns no value.

**named function arguments** - you can pass arguments in any order:
`foo(healthStatus = "status", auraColor = "NONE", name = "Madrigal", isBlessed = true)`

## Anonymous Function and the Function Type
  - have no name, are commonly passed around or returned from other functions
  - similar to normal function, an anonymous function will only execute when it's called with ()
  - variable of function type can hold anonymous function
  - last line in method body is used as return value & writing `return` keyword is not allowed to reduce confusion between functions
  - it can accept at-least 0 arguments
  - `it` can be used when there is only one parameter involved however bad for readability when complexity involved
  - `it` cannot be used when more than one parameter involved then you use multiple named parameters
  - anonymous functions are referred to as **lambdas** and their defintion as *lambda expressions*.
  - anonymous function return is referred to as *lambda result*
  - lambda is an object instance on the JVM so it has a memory cost for itself and all the variables accessible to it but _inline_ enables to remove lambda memory overhead. 
  - an inline lambda body is just copy pasted by the compiler instead of invoking an object instance
  - function references (::) can be useful for named function
  - you can return a function
  - lambdas also called closures because they "close over" the variables in the outer scope that they are defined within
  
### To count letters in a string
```
val numLetters = "Mississippi".count()
print(numLetters)
```

### To provide logic to a function via anonymous function:
```
val numLetters = "Mississippi".count({ letter ->
        letter == 's'
    })
    print(numLetters)
```

### Calling an anonymous function:
```
{
  val currentYear = 2019
  "Welcome to My World! (copyright $currentYear)"
}()
```

### An anonymous function that can return string and stored in a variable:
```
val greetingFunction: () -> String = {
        val currentYear = 2018
        "Welcome to SimVillage, Mayor! (copyright $currentYear)"
}
```

### A single parameter anonymous function:
```
val greetingFunction: (String) -> String = { playerName ->
  val currentYear = 2018
  "Welcome to SimVillage, $playerName! (copyright $currentYear)"
}
```

### A single parameter anonymous function using it keyword:
```
val greetingFunction: (String) -> String = {
        val currentYear = 2018
        "Welcome to SimVillage, $it! (copyright $currentYear)"
}
```

### An anonymous function type can be inferred:
```
val greetingFunction = {
        val currentYear = 2018
        "Welcome to SimVillage, Mayor! (copyright $currentYear)"
}
```

### Type inference using parameters type:
```
val greetingFunction = { playerName: String, numBuildings: Int ->
        val currentYear = 2018
        println("Adding $numBuildings houses")
        "Welcome to SimVillage, $playerName! (copyright $currentYear)"
}
```

### Passing lambda as argument to another function:
```
fun main(args: Array<String>) {
    val greetingFunction = { playerName: String, numBuildings: Int ->
        val currentYear = 2018
        println("Adding $numBuildings houses")
        "Welcome to SimVillage, $playerName! (copyright $currentYear)"
    }
    runSimulation("Guyal", greetingFunction)
}

fun runSimulation(playerName: String, greetingFunction: (String, Int) -> String) {
    val numBuildings = (1..3).shuffled().last()   // Randomly selects 1, 2, or 3
    println(greetingFunction(playerName, numBuildings))
}
```

### Omitting parentheses when function type is last argument:
```
fun main(args: Array<String>) {
    runSimulation("Guyal") { playerName, numBuildings ->
        val currentYear = 2018
        println("Adding $numBuildings houses")
        "Welcome to SimVillage, $playerName! (copyright $currentYear)"
   }
}

fun runSimulation(playerName: String, greetingFunction: (String, Int) -> String) {
   val numBuildings = (1..3).shuffled().last()   // Randomly selects 1, 2, or 3
   println(greetingFunction(playerName, numBuildings))
}
```

### An inline method:
```
inline fun runSimulation(playerName: String,
                        greetingFunction: (String, Int) -> String) {
    val numBuildings = (1..3).shuffled().last()   // Randomly selects 1, 2, or 3
    println(greetingFunction(playerName, numBuildings))
}
```

### Passing function reference:
```
fun printConstructionCost(numBuildings: Int) {
    val cost = 500
    println("construction cost: ${cost * numBuildings}")
}

fun main(args: Array<String>) {
    runSimulation("Guyal", ::printConstructionCost) { playerName, numBuildings ->
        val currentYear = 2018
        println("Adding $numBuildings houses")
        "Welcome to SimVillage, $playerName! (copyright $currentYear)"
    }
}
```

### Return a function and lambda as Closure
```
fun runSimulation() {
    val greetingFunction = configureGreetingFunction()
    println(greetingFunction("Guyal"))
}

fun configureGreetingFunction(): (String) -> String {
    val structureType = "hospitals"
    var numBuildings = 5
    return { playerName: String ->
        val currentYear = 2018
        numBuildings += 1
        println("Adding $numBuildings $structureType")
        "Welcome to SimVillage, $playerName! (copyright $currentYear)"
    }
}
```

## TOPIC

Within **_let, it_** is a reference to the variable on which let is called, and is guaranteed to be non-null

In Kotlin, all exceptions are **unchecked**. This means that the Kotlin compiler does not force you to wrap all code that could produce an exception in a try/catch statement.

Kotlin also provides the safe conversion functions **_toDoubleOrNull_** and **_toIntOrNull_**

**apply**  - returns the object (receiver) itself, passes nothing
**let** - returns the result of lambda, passes in receiver as it (read-only)
**run** - similar to apply except that doesn’t return the object, can also be used to execute a function reference on a receiver
**with** - same as run except you pass argument as first parameter to lambda, should be avoided
**also** - same as let but returns the object rather than result of lambda
**takeif** - evaluates a condition provided in a lambda (called predicate). If the condition evaluates as true, the receiver is returned otherwise null is returned

One of these safe index access functions, **getOrElse**
Another safe index access function, **getOrNull**, returns null instead of throwing an exception
**mutableListOf, listOf**
You could create a read-only version of the mutable patronList using **toList**

**forEach, forEachIndexed**


