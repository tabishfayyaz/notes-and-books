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

Kotlin uses the **_Unit_** return type to signify exactly this: a function that returns no value. Another type that is related to Unit is the **_Nothing_** type

**named function arguments** - you can pass arguments in any order:
`foo(healthStatus = "status", auraColor = "NONE", name = "Madrigal", isBlessed = true)`

### Anatomy of a Function:
```
private fun formatHealthStatus(healthPoints: Int, isBlessed: Boolean) : String {
}
```

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
- _null coalescing operator_ (?:) if the expression to the left of ?: is not null, the elvis operator returns it, otherwise it returns the expression to the right. Note that the right-hand side expression is evaluated only if the left-hand side is null.
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

### Safe Conversion that will Throw Exception:
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

### Examples of Mutator Functions:
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

### Declare Primitive Integer Array:
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

## Maps
- The keys of map are gauranteed to be unique just like the elements of a set
- If a key is already in map, the key-value pair will be replaced with new one
- Map accessor functions: ```[] (get/index operator)```, ```getValue```, ```getOrElse```, ```getOrDefault```

### Creating a map:
```
val patronGold = mapOf("Eli" to 10.5, "Mordoc" to 8.0, "Sophie" to 5.5)

val patronGold = mapOf(Pair("Eli", 10.75),
    Pair("Mordoc", 8.00),
    Pair("Sophie", 5.50))
    
val patronGold = mutableMapOf<String, Double>()
```

### Mutable map mutator functions:
```
patronGold["Mordoc"] = 5.0  //assignment
patronGold += "Eli" to 5.0  //plus assign operator
patronGold += mapOf("Eli" to 7.0,"Mordoc" to 1.0, "Jebediah" to 4.5)  //plus assign operator
patronGold.put("Mordoc", 5.0) //put
patronGold.putAll(listOf("Jebediah" to 5.0, "Sahara" to 6.0)) //putAll
patronGold.getOrPut("Randy"){5.0} //getOrPut
val mordocBalance = patronGold.remove("Mordoc"){} //remove
val newPatrons = mutableMapOf("Mordoc" to 6.0, "Jebediah" to 1.0) - "Mordoc"  //minus operator
mutableMapOf("Mordoc" to 6.0, "Jebediah" to 1.0) -= "Mordoc" //minus assign operator
mutableMapOf("Mordoc" to 6.0, "Jebediah" to 1.0).clear(){}  //clear operator
```

## Classes
- _class functions:_ functions defined within a class
- _class properties:_ data defintions within a class
- When an instance of a class is constructed, all of its properties must have values i.e. class properties must be assigned an initial value
- for each property you define, Kotlin will generate a _field_, a _getter_ and if needed a _setter_. 
- Data for the property is stored in the field. You cannot directly define a field on a class. Kotlin encapsulates the fields for you, protecting the data in the field and exposing it via getters and setters
- You can define your own custom getters & setters
- Any function or property without a visibility modifier is public
- visibility modifiers: public (default, accessible outside of the class), private (accessible only within the same class), protected (accessible only within the same class or its subclass), internal (accessible within the same module)
- Internal visibility is useful for sharing classes within a module (include such things as source code, build scripts, unit tests, and so on). It is a great choice for building libraries in Kotlin as your classes are shared only within a module.
- Java by default uses _package private_ visibility but Kotlin differs from Java in that aspect
- The difference between using the _var_ and _val_ keyword is the absence of a setter in decompiled bytecode

### Calling a class Primary Constructor:
```
val player = Player()
```

### Defining a class:
```
class Player {
    val name = "madrigal"
    fun castFireball(numFireballs: Int = 2) =
            println("A glass of Fireball springs into existence. (x$numFireballs)")
}
```

### Custom getter and a private setter:
```
class Player {
    var name = "madrigal"
        get() = field.capitalize()
        private set(value) {
            field = value.trim()
        }    
}
```

### Prevent a check and act null race condition:
```
fun printWeaponName() {
  weapon?.also {
    println(it.name)  //it variable is guaranteed to not be changed by another part of the program
  }
}
```
## Initialization
- _instantiated_: when memory is allocated, _initialized_: when value is assigned however the typical usage of initialization is when everything required to make a variable, property or class instance ready to use whereas instantiation is creating an instance of a class
- constructor allows its caller to specify the initial values that an instance of a class will require in order to be constructed
- temporary variables, including parameters that you do not need to reference more than once, are often given a name starting with an underscore to signify they are single-use
- for class properties that use the default getter & setter, kotlin allows to specify both in one definition, rather than having to assign them using temporary variables
- for each constructor parameter you can specify whether it is writable (_var_) or read-only (_val_)
- There are two flavors of constructors (primary & secondary), _primary:_ these parameters are requires for any instance of this class, _secondary:_ alternative ways to construct the class however requirements of the primary constructor are still met
- Secondary constructors cannot be used to define properties like primary constructors can. Class properties have to be defined in the primary constructor or at the class level
- Kotlin provides named constructor arguments similar to named arguments for calling functions
- _initializer block_ is a way to set up variables/values as well as perform validation. The initializer code is executed when the class is constructed
- a property must be initialized when the class instance is constructed (this is part of Kotlin's null safety system because that means that all non-nullable properties of a class are initialized and can reference any property of that class instance)
- _late initialization:_ helpful for bending initilization rules in frameworks like Android where constructor of an _Activity_ is not in our control
- One could use nullable to initialize properties with null but that would be more taxing on code/maintenance
- _lazy initialization:_ hold off initializing a variable until it is accessed for the first time, implemented in Kotlin using a mechanism called **delegate** (_by_ keyword)
- Kotlin includes some delegates that are already implemented for us: **lazy** being one of them that takes a lambda. The code in lambda is executed only once and future access depends on a cached result

### Primary Constructor:
```
class Player(_name: String,
            _healthPoints: Int,
            _isBlessed: Boolean,
            _isImmortal: Boolean) {
    var name = _name
        get() = field.capitalize()
        private set(value) {
            field = value.trim()
        }

    var healthPoints = _healthPoints
    val isBlessed = _isBlessed
    private val isImmortal = _isImmortal
}

fun main(args: Array<String>) {
    val player = Player("Madrigal", 89, true, false)
}

```

### Primary constructor with default and custom getter/setter:
```
class Player(_name: String, var healthPoints:Int, val isBlessed:Boolean, private val isImmortal:Boolean) {
    var name = _name
        get() = field.capitalize()
        private set(value) {
            field = value.trim()
        }
}
```

### Defining a secondary constructor:
```
class Player(_name: String,
            var healthPoints: Int
            val isBlessed: Boolean
            private val isImmortal: Boolean) {
    var name = _name
        get() = field.capitalize()
        private set(value) {
            field = value.trim()
        }

    constructor(name: String) : this(name, healthPoints = 100, isBlessed = true, isImmortal = false)
}

fun main(args: Array<String>) {
    val player = Player("Madrigal")
}
```

### Defining a constructor with default argument:
```
class Player(_name: String,
            var healthPoints: Int = 100
            val isBlessed: Boolean
            private val isImmortal: Boolean) {
    var name = _name
        get() = field.capitalize()
        private set(value) {
            field = value.trim()
        }

    constructor(name: String) : this(name,            
            isBlessed = true,
            isImmortal = false) {
        if (name.toLowerCase() == "kar") healthPoints = 40
    }
}
```

### Defining an initializer block:
```
class Player(_name: String,
            var healthPoints: Int = 100
            val isBlessed: Boolean
            private val isImmortal: Boolean) {
    var name = _name
        get() = field.capitalize()
        private set(value) {
            field = value.trim()
        }

    init {
        require(healthPoints > 0, { "healthPoints must be greater than zero." })
        require(name.isNotBlank(), { "Player must have a name." })
    }    
}
```

### Property initialization:
```
class Player(_name: String,
            var healthPoints: Int = 100
            val isBlessed: Boolean
            private val isImmortal: Boolean) {
    var name = _name
        get() = "${field.capitalize()} of $hometown"
        private set(value) {
            field = value.trim()
        }

    val hometown = selectHometown()
    ...
    private fun selectHometown() = File("data/towns.txt")
            .readText()
            .split("\n")
            .shuffled()
            .first()
}
```
### late initialization:
```
class Player {
        lateinit var alignment: String
        fun determineFate() {
            alignment = "Good"
        }

        fun proclaimFate() {
            if (::alignment.isInitialized) println(alignment)
        }
    }
```
### lazy initialization:
```
class Player(_name: String,
            var healthPoints: Int = 100
            val isBlessed: Boolean
            private val isImmortal: Boolean) {
    var name = _name
        get() = "${field.capitalize()} of $hometown"
        private set(value) {
            field = value.trim()
        }

    val hometown = by lazy { selectHometown() }   //hometown remains uninitialized until it is referenced for the first time
    ...
    private fun selectHometown() = File("towns.txt")
            .readText()
            .split("\n")
            .shuffled()
            .first()
}
```

## Inheritance
- to define hierarchical relationships between types and define common things in a shared class
- classes are closed by default i.e. they prohibit subclassing so it has be to be marked with _open_ to allow subclassing
- a function also has to be marked _open_ in order to override it
- a function cannot be overridden if _final_ keyword is specified
- you can check whether an object is instance of a class using _is_ operator
- you can cast an object to another one using _as_ operator
- every class in Kotlin comes from a common subclass **Any** and that is why you can use it as function parameter
- **Any** enables kotlin to be platform independent depending upon what you target for compilation e.g. _toString_ implementation is java.lang.Object.toString when you target JVM, it could be something completely different if compiling to JavaScript
- **Any** provides default implementation of _toString_, _equals_ and _hashCode_ which improves the speed a value can be retrieved with a key when using a Map

### Declaring a subclass and open function to override:
```
open class Room(val name: String) {
    fun description() = "Room: $name"

    open fun load() = "Nothing much to see here..."
}

class TownSquare : Room("Town Square") {
    override fun load() = "The villagers rally and cheer as you enter!"
}
```

### type checking with when:
```
var townSquare = TownSquare()
var className = when(townSquare) {
    is TownSquare -> "TownSquare"
    is Room -> "Room"
    else -> throw IllegalArgumentException()
}
```

## Objects
- _object_ keyword is used to specify that a class will be single instance (singleton). The instance will persist as long as program is running. An _object_ represents a single static instance.
- Three ways to use object keyword: object declarations, object expressions, companion objects (always declared inside of another class)
- The main use for companion objects is to replace static fields/methods known from Java
- As the object is instantiated for you, you do not add a custom constructor but need an initializer block
- A function defined in an object declaration is called using name of the object and not an instance of a class
- object expression if declared at the file level is initialized immediately or if declared within another class, it is initialized when its enclosing class is initialized
- companion objects are initialized when its enclosing class is initialized or when one of its properties/function are accessed directly
- Data classes are classes designed specifically for holding data. it's _toString_ implementation is more useful
- By default, objects are compared by references (==) as that is default implementation of **equals** in _Any_ however data classes provide an implementation that bases equality on all properties declared in primary constructor
- Data classes also make it easy to create a new copy of an object
- Data classes enable destructuring declaration (creating multiple variables at once) out of the box
- Data classes must have: primary constructor with at-least one parameter and parameters be marked with either a val/var, they cannot be abstract, open, sealed or inner
- Enumerated class is a special type of class for defining a collection of constants, are more descriptive than other types of constants and they can also hold function declarations
- Common operators that can be over-loaded: plus for +, plusAssign for +=, equals for ==, compareTo for >, get for [], rangeTo for .., contains for in
- ADB (abstract data types) allow you to represent a closed set of possible subtypes that can be associated with a given type. Enum classes are a simple form of ADT
- Sealed classes (https://stackoverflow.com/a/50779528) are designed to be used when there are a very specific set of possible options for a value, and where each of these options is functionally different. Sealed class allow you to exhaust possibilities
- Anytime you have multiple options and they only different in the meaning of the data, you may be better off using enum classes
- Anytime you have an unknown number of options, you can not use a sealed class because this will stop you adding options outside of the original source file

### Object Declaration (State Management):
```
fun main(args: Array<String>) {
    Game.play()
}

object Game {
    init {
        println("Welcome, adventurer.")
    }

    fun play() {
        while (true) {
        }
    }
}
```

### Object Expression (One-Off purpose):
```
val abandonedTownSquare = object : TownSquare() {
        override fun load() = "You anticipate applause, but no one is here..."
}
```

### Companion Object (initialization tied to a class instance):
```
class PremadeWorldMap {
  companion object {
    private const val MAPS_FILEPATH = "nyethack.maps"
      fun load() = File(MAPS_FILEPATH).readBytes()
  }
}
```

### Nested Class:
```
object Game {
    private class GameInput(arg: String?) {
        private val input = arg ?: ""
        val command = input.split(" ")[0]
        val argument = input.split(" ").getOrElse(1, { "" })
    }
}
```

### Defining a data class:
```
data class Coordinate(val x: Int, val y: Int) {
    val isInBounds = x >= 0 && y >= 0
}
```

### Making a class support destructuring declaration:
```
class PlayerScore(val experience: Int, val level:Int ){
        operator fun component1() = experience    //under the hood how destructuring declaration is implemented
        operator fun component2() = level
    }

    val (experience, level) = PlayerScore(1250, 5)
```

### Defining a function in an enum:
```
enum class Direction(private val coordinate: Coordinate) {
    NORTH(Coordinate(0, -1)),
    EAST(Coordinate(1, 0)),
    SOUTH(Coordinate(0, 1)),
    WEST(Coordinate(-1, 0));

    fun updateCoordinate(playerCoordinate: Coordinate) =
            Coordinate(playerCoordinate.x + coordinate.x, playerCoordinate.y + coordinate.y)
}

data class Coordinate(val x: Int, val y: Int) {
    val isInBounds = x >= 0 && y >= 0
}
```

### Overloading + operator:
```
enum class Direction(private val coordinate: Coordinate) {
    NORTH(Coordinate(0, -1)),
    EAST(Coordinate(1, 0)),
    SOUTH(Coordinate(0, 1)),
    WEST(Coordinate(-1, 0));

    fun updateCoordinate(playerCoordinate: Coordinate) = coordinate + playerCoordinate
}

data class Coordinate(val x: Int, val y: Int) {
    val isInBounds = x >= 0 && y >= 0

    operator fun plus(other: Coordinate) = Coordinate(x + other.x, y + other.y)
}
```

### Sealed class:
```
sealed class ScreenState {
    class Error : ScreenState()
    class Loading : ScreenState()
    data class Data(val someData: SomeData) : ScreenState()
}
```

## Interfaces & Abstract Classes
- Interface: specifying _what_ without the _how_, Abstract class: hybrid between interface and class
- Use : operator to implement an interface
- You can provide a default implementation for a property getters & functions in an interface
- Abstract class cannot be instantiated but provide function implementation, they can also include abstract functions

### Define an interface
```
interface Fightable {
    var healthPoints: Int
    val diceCount: Int
    val diceSides: Int
    val damageRoll: Int

    fun attack(opponent: Fightable): Int
}
```

### Implementing an interface
```
class Player(_name: String,
        override var healthPoints: Int = 100,
        var isBlessed: Boolean = false,
        private var isImmortal: Boolean) : Fightable {
}
```

### Defining an abstract class
```
abstract class Monster(val name: String,
                       val description: String,
                       override var healthPoints: Int) : Fightable {

    override fun attack(opponent: Fightable): Int {
        val damageDealt = damageRoll
        opponent.healthPoints -= damageDealt
        return damageDealt
    }
}
```

### Subclassing an abstract class
```
class Goblin(name: String = "Goblin",
             description: String = "A nasty-looking goblin",
             healthPoints: Int = 30) : Monster(name, description, healthPoints) {

    override val diceCount = 2
    override val diceSides = 8
}
```

## Generics
- A data of some type that is not known when we write the class
- Lists can hold any type because of _generics_
- A _generic type_ is a class that accepts an input of any type in its constructor
- Generics types, like other types in Kotlin, support type inference
- TODO: in and out, reified keyword

### List of Generics
```
val listOfInts: List<Int> = listOf(1,2,3)
val listOfStrings: List<String> = listOf("string one", "string two")
val listOfRooms: List<Room> = listOf(Room(), TownSquare())
```

### Creating a Generic Type:
```
class LootBox<T>(item: T) {
    private var loot: T = item
}
```

### Adding a Generic function:
```
class LootBox<T>(item: T) {
    var open = false
    private var loot: T = item

    fun fetch(): T? {
       return loot.takeIf { open }
    }
}
```

### Using multiple generic type parameters:
```
fun <R> fetch(lootModFunction: (T) -> R): R? {
        return lootModFunction(loot).takeIf { open }
}
```

### Constraining a generic parameter to specific class:
```
class LootBox<T : Loot>(item: T) {

}
```

### Adding vararg:
```
class LootBox<T : Loot>(vararg item: T) {

}
```

### Adding a get operator:
```
class LootBox<T : Loot>(vararg item: T) {
   operator fun get(index: Int): T? = loot[index].takeIf { open }
}
```

## Extensions
- allow you to add functionality to a type without directly modifying the type's definition, you can use them with own types or from the Kotlin standard library (List, String)
- a good option when you want to add functionality to a class you do not control or that is ineligible for subclassing

### Adding extension to string:
```
fun String.addEnthusiasm(amount: Int = 1) = this + "!".repeat(amount)
```

### Extending Any (callable on all types e.g. Int)
```
fun Any.easyPrint() = println(this)
```

### Make a function chainable:
```
fun Any.easyPrint(): Any {
    println(this)
    return this
}
```

## Functional Programming
- functional programming style relies on data that is returned from a small number of higher-order functions (functions that accept or return another function) designed specifically to work on collections, and it favors composing chains of operations
- Kotlin supports multiple programming style so you can mix OOP and functional programming
- Three broad categories of functional programs: _transforms, filters_ and _combines_
- Functions in functional programming are also designed to be _composable_, meaning that simple functions can be combined to build complex behavior

### Converting a list of animals to babies (transform):
```
val animals = listOf("zebra", "giraffe", "elephant", "rat")
val babies = animals
    .map{ animal -> "A baby $animal" }
    .map{ baby -> "$baby, with the cutest little tail ever!"}
println(babies)
```

## Java Interoperability
- Kotlin compiles down to java bytecode and kotlin and java files can exist in the same project, side by side
- You can invoke java methods from Kotlin and vice-versa this means existing java libraries can be used from Kotlin
- String!: The exclamation mark means that the return value could either be String or String?
- Kotlin types often correspond one to one with Java types e.g a String in Kotlin is a String when compiled down to Java
- Primitive types are not objects in Java, but all types are objects in Kotlin - including the basic data types. Kotlin compiler will map java primitives onto the most similar kotlin type
- Kotlin gives you the power of objects when you want them but the performance of primitive types when you need them (types are mapped back to their java counterparts at runtime)
- Java uses fields and typically gates access via accessor and mutator methods. Kotlin features properties which restrict access to backing fields and may automatically expose accessors and mutators
- Kotlin can by pass the need for using getter/setter syntax, meaning that you can use syntax that looks like you are accessing fields or properties directly while still maintaining encapsulation
- A kotlin file can include classes, functions, and variables - all at the top level of the file. Top-level functions defined in Kotlin are represented as static methods in Java
- Consider using _@JvmOverloads_ for an API that may be exposed to Java consumers so it is robust for both Java & Kotlin developers
- Recall that Kotlin and Java handle class-level variables quite differently: Java uses fields with getter and setter methods, while Kotlin has properties with backing fields.
- You can apply the _@JvmField_ annotation to a Kotlin property to expose its backing field to Java consumers and avoid the need for a getter method
- The _@JvmStatic_ annotation works like _@JvmField_ to allow direct access to functions defined on companion objects
- Function types is made possible via the -> operator

### Referencing a top-level Kotlin function from java
```
public class Jhava {
    public static void main(String[] args) {
        System.out.println(HeroKt.makeProclamation());  // Kotlin compiler creates a class called HeroKt (to associate Hero.java)
    }
}
```

### Specifying compiled class name using JvmName at top of Hero.kt:
```
@file:JvmName("Hero")

fun main(args: Array<String>) {
}

fun makeProclamation() = "Greetings, beast!"
```

### A function with default parameters:
```
fun handOverFood(leftHand: String = "berries", rightHand: String = "beef") {
    println("Mmmm... you hand over some delicious $leftHand and $rightHand.")
}
```

### Java has no concept of default method parameters so to over come that (Hero.kt):
```
@JvmOverloads
fun handOverFood(leftHand: String = "berries", rightHand: String = "beef") {
    println("Mmmm... you hand over some delicious $leftHand and $rightHand.")
}
```

### Applying the @JvmField annotation:
```
class Spellbook {
    @JvmField   //now you can access spells directly from java code
    val spells = listOf("Magic Ms. L", "Lay on Hans") 
}
```

### Adding the @JvmField annotation to the member of a companion object:
```
class Spellbook {
    @JvmField
    val spells = listOf("Magic Ms. L", "Lay on Hans")

    companion object {
        @JvmField
        val MAX_SPELL_COUNT = 10
    }
}
```

## Coroutines
- A thread is a pipeline that handles a sequence of work to be performed.
- The main thread of an Android application is reserved for work that keeps UI responsive: handling button presses, scroll updates, textbox updates
- If thread cannot move forward to the next work to process until the current (possibly long - running) completes this is called _blocking a thread_
