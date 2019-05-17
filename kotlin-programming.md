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
  - anonymous function can modify and reference variable outside of its scope
  - You cannot pass a function as parameter or define function variable in Java 8 the way you can in Kotlin, you always need to define an interface to be able to pass around an anonymous inner class
  
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

## Null Safety & Exceptions
- if a _var_ or _val_ can accept null then kotlin requires a special declaration which helps with avoiding crashes
- _null_ is not 0 but the absence of any value
- _nullable_: can be assigned a value of null, _non-nullable_: cannot be assigned a value of null
- Kotlin is a _compiled language_ (the program is translated into machine-language instructions before execution by the compiler) so compiler checks whether null is assigned correctly type or not. 
- Compile-time errors are preferable to _runtime errors_ (mistakes that happen when program is already running) so you find the problem earlier than later
- Use non-nullable type, if at all possible
- _safe call operator_ (?) will only call the function if and only if the variable it acts on is not null
- within **_let, it_** is a reference to the variable on which let is called, and is guaranteed to be non-null
- **_let_** returns the results of your expression implicitly
- _double bang operator_ or _non-null assertion operator_ (!!.): when a call is made to a non-existent thing you demand that a null pointer exception be thrown, generally advised not to be used unless you want to assert for non-null
- _null coalesging operator_ (?:) if thing on the lefthand side of me is null, do the thing on the righthand side instead, sort of like providing a default
- in kotlin variables are non-nullable by default
- exception represents an unrecoverable state unless it is handled, throwing an exception signals that the issue must be handled before execution continues
- in Kotlin, all exceptions are **unchecked** so you are not forced by Kotlin to wrap all code that could produce an exception in a try/catch statement
- modern languages experience showed that **checked** exceptions lead to problems: code duplication, complexity in error recovering logic and swallowed exceptions
- precondition are conditions that must be true before proceeding:
- `checkNotNull` (throw IllegalStateException if argument is null)
- `require` (throw IllegalArgumentException if argument is false)
- `requireNotNull` (throw IllegalArgumentException if argument is null otherwise return the non-null value)
- `error` (throw IllegalArgumentException with a provided message if argument is null)
- `assert` (throw AssertionError if argument is false and the assertion compiler flag is enabled)


### A function that returns nullable string
```
public fun readLine(): String?
```

### Using the safe call operator:
```
fun main(args: Array<String>) {
    var beverage = readLine()?.capitalize()
    println(beverage)
}
```

### Using let with a safe call operator:
```
var beverage = readLine()?.let {
        if (it.isNotBlank()) {
            it.capitalize()
        } else {
            "Buttered Ale"
        }
    }
```

### Using the double-bang operator:
```
var beverage = readLine()!!.capitalize()
```

### Using the null coalescing operator:
```
val beverageServed: String = beverage ?: "Buttered Ale"
```

### null coalescing operator with let function:
```
beverage?.let {
        beverage = it.capitalize()
    } ?: println("I can't do that without crashing - beverage was null!")
```

### throwing an illegal state exception:
```
fun proficiencyCheck(swordsJuggling: Int?) {
    swordsJuggling ?: throw IllegalStateException("Player cannot juggle swords")
}
```

### to define a custom exception inherited from some other exception:
```
class UnskilledSwordJugglerException() : IllegalStateException("Player cannot juggle swords")
```

### try-catch block:
```
try {

    } catch (e: Exception) {
        println(e)
    }
```

### preconditions:
```
checkNotNull(swordsJuggling, { "Player cannot juggle swords" })
require(swordsJuggling >= 3, { "Juggle at least 3 swords to be exciting." })
```

## Strings
- escape sequences: `\t` tab character, `\b` backspace character, `\n` newline character, `\r` carriage return, `\"` double quotation mark, `\'` single quotation mark, `\\` backslash, `\$` dollar sign, `\u` unicode character
- whether you define one with _var_ or _val_ all strings in Kotlin are immutable just like java, anything that seems like is changing value of string (like `replace`) in reality creates a new string
- for checking string equality use `==` for checking string referential equality use `===`, java uses `==` for referential equality of objects
- a **Char** is a Unicode Character which is designed to support diverse languages, string consists of an ordered sequence of characters
- not all characters are available on keyboard so then you use unicode



### replacing a string:
```
private fun toDragonSpeak(phrase: String) =
    phrase.replace(Regex("[aeiou]")) {
        when (it.value) {
            "a" -> "4"
            "e" -> "3"
            "i" -> "1"
            "o" -> "0"
            "u" -> "|_|"
            else -> it.value
        }
    }
```

### multiple ways to declare a character:
```
    val capitalA: Char = 'A'
    val unicodeCapitalA: Char = '\u0041'
```

### traversing a string/list:
```
"Dragon's Breath".forEach {
        println("$it\n")
    }
```

## Numbers
- all numeric types in Kotlin are signed, Byte: 8 Bits; Short: 16 Bits; Int: 32 Bits; Long: 64 Bits; Float: 32 Bits; Double: 64 Bits
- neighter _Short_ or _Byte_ is a common use case
- Binary Operations: _Integer.toBinaryString, shl(bitcount), shr(bitcount), inv(), xor(number), and(number)_
- _toDoubleOrNull_ and _toIntOrNull_ are used for safe conversion

### Formatting a Double:
```
println("Remaining balance: ${"%.2f".format(remainingBalance)}")
```

### Double to Int:
```
val remainingSilver = (remainingBalance % 1 * 100).roundToInt()
```

### Safe Conversion that will Throw Exception
```
val gold: Int =  "5.91".toIntOrNull() ?: 0
```

## Standard Functions
- **apply**  : returns the object (receiver) itself, passes nothing
- **let** : returns the result of lambda, passes in receiver as it (read-only)
- **run** : similar to apply except that doesn’t return the object, can also be used to execute a function reference on a receiver. Chained calls using run are easier to read and follow than nested function calls.
- **with** : same as run except you pass argument as first parameter to lambda, should be avoided
- **also** : same as let but returns the object rather than result of lambda
- **takeif** : evaluates a condition provided in a lambda (called predicate). If the condition evaluates as true, the receiver is returned otherwise null is returned
- _relative scoping:_ all the function calls within the lamda are now called relative to the receiver
 
### Example for apply:
```
val menuFile = File("menu-file.txt").apply {
        setReadable(true)  // Implicitly, menuFile.setReadable(true)
        setWritable(true)  // Implicitly, menuFile.setWritable(true)
        setExecutable(false)  // Implicitly, menuFile.setExecutable(false)
}
```

### Example for let:
```
val firstItemSquared = listOf(1,2,3).first().let {
        it * it
}
```

### Example for run:
```
val menuFile = File("menu-file.txt")
     val servesDragonsBreath = menuFile.run {
         readText().contains("Dragon's Breath")
     }
```

### _run_ on a function reference:
```
"Polarcubis, Supreme Master of NyetHack"
        .run(::nameIsLong)
        .run(::playerCreateMessage)
        .run(::println)
```

## List and Sets:
- list, sets and map come in two distinct varieties: mutable and read-only
- safe index access functions: **getOrElse**, **getOrNull**
- add, addAll, removeIf, +=, -= are called _mutator functions_ as they change contents of a mutable list

### Creating a read-only or mutable list:
```
val patronList: List<String> = listOf("Eli", "Mordoc", "Sophie")
val patronList: List<String> = mutableListOf("Eli", "Mordoc", "Sophie")
```

### _first_ or _last_ element:
```
patronList.first()
patronList.last()
```

### Example of getOrElse:
```
val patronList = listOf("Eli", "Mordoc", "Sophie")
patronList.getOrElse(4) { "Unknown Patron" }
```

### Example of getOrNull:
```
val fifthPatron = patronList.getOrNull(4) ?: "Unknown Patron"
fifthPatron
```

### Examples of Mutator Functions 
```
patronList[4] = "Reggie"
patronList.add("Reggie")
patronList.add(0, "Reggie")
patronList.addAll(listOf("Reginald", "Alex"))
mutableListOf("Eli","Mordoc", "Sophie") += listOf("Alex", "Shruti")
mutableListOf("Eli", "Mordoc", "Sophie") += "Reginald"

val patronList = mutableListOf("Eli","Mordoc","Sophie")
patronList -= listOf("Eli", Mordoc")

mutableListOf("Eli", "Mordoc", Sophie").clear()

val patronList = mutableListOf("Eli", "Mordoc", "Sophie")
patronList.removeIf { it.contains("o") }
```

## Iterations
- _List, Set, Map, IntRange (0..9)_ are category of types called _Iterable_
- _Set_ also comes in read - only and mutable flavors
- **toSet, toList, toMutableSet, toMutableList** can be used to convert between collections
- _IntArray_ is backed by a primitive type when compiled to bytecode unlike _List_
- It is up to you to use _List_ in an immutable fashion, Kotlin does not enforce it

### Examples of Iteration:
```
for (patron in patronList) {
        println("Good evening, $patron")
}
    
patronList.forEach { patron ->
        println("Good evening, $patron")
}

patronList.forEachIndexed { index, patron ->
        println("Good evening, $patron - you're #${index + 1} in line.")
}
```

### Reading file into List:
```
val menuList = File("data/tavern-menu-items.txt").readText().split("\n")
```

### Destructuring a List:
```
val (type, name, price) = menuData.split(',')  // type, name, price are string variables

val (goldMedal, _, bronzeMedal) = patronList  // the middle element is skipped
```

### Create a Set:
```
val planets = setOf("Mercury", "Venus", "Earth")
```

### Convert a set back to list and remove duplicates:
```
val patrons = listOf("Eli Baggins", "Eli Baggins", "Eli Ironfoot").toSet().toList()

val patrons = listOf("Eli Baggins", "Eli Baggins", "Eli Ironfoot").distinct() // achieves same result
```

### Declare Primitive Integer Array
```
static void displayPlayerAges(int[] playerAges) {
        for(int i = 0; i < ages.length; i++) {
            System.out.println("age: " + ages[i]);
        }
}

val playerAges: IntArray = intArrayOf(34, 27, 14, 52, 101)
displayPlayerAges(playerAges)
```

### Immutability not enforced example:
```
 var myList: List<Int> = listOf(1,2,3)
    (myList as MutableList)[2] = 1000
```
