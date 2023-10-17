

### Basic Syntax:

**Write a program to print "Hello, World!" to the console.**
```
fun printHelloWorld(){
    System.out.println("Hello World")
}
```

**Create a program to calculate the area of a rectangle (length * width) and print the result.**
```
fun rectArea(length:Int, width:Int) : Int {
    return length * width
}
```   

**Write a function to find the factorial of a number using recursion.**
```
fun factorial(number:Int) : Int {
    var result = 1;
    for (count in 1 .. number){
        result *= count
    }
    return result
}
```
**Create a program that converts temperatures from Celsius to Fahrenheit and vice versa.**
```
fun convertTemperature(temperature: Double, toCelsius: Boolean): Double {
    if (toCelsius){
        return (temperature - 32) * 5/9
    } else {
        return temperature * (9/5) + 32 
    }
}
```

**Write a program to find the sum of all even numbers between 1 and 100.**
```
fun sumEvenTo100() : Int {
    val numbers = 1..100

    // or {element -> element % 2 == 0} to rename "it" for better readability
    val evenNumbers = numbers.filter {it % 2 == 0}  
    var sum = 0;
    for (number in evenNumbers){
        sum += number
    }
    return sum;
}
```

**Write a generic function to print contents of an Array**
```
fun <T> printArray(values: Array<T>){
    for (value in values){
        print(value.toString() + " ")
    }
}
```

### Data Types and Variables:

**Create a Kotlin program to swap the values of two variables without using a temporary variable.**
```
   fun swap(a: Int, b: Int) {
    var value1 = a
    var value2 = b
    println("value1: " + value1 + " value2 " + value2)
    value1 = value2.also { value2 = value1 }
    println("value1: " + value1 + " value2 " + value2)
}
```
**Write a program to check if a given number is prime or not using Kotlin.**
```
    /*
        for (i in 1 .. 25){
            println("${i} isPrime: ${isPrime(i)}")
        }
    */

   fun isPrime(number: Int) : Boolean {
    var divisor = 2
    while (divisor < number){
        if (number % divisor == 0)
            return false
        ++divisor
    }

    return true
}
```

**Create a program that converts a decimal number to a binary representation in Kotlin.**
```
fun convertDecimalToBinary(number: Int): String{
    
    if (number == 0)
    return "0"
    /*
        When we start dividing the decimal number by 2, the first remainder we obtain corresponds to the least significant bit (LSB) of the binary representation. 
        This is because it represents the remainder when dividing by the smallest power of 2. The remainder from the last division represents the most significant bit (MSB) of the binary number.
     */
    var decimal = number;
    var result = ""
    while(decimal > 0){
        var remainder = decimal % 2
        result = remainder.toString() + result
        decimal = decimal / 2
    }
    return result
}
```


### Collections:

**Write a Kotlin function to find the maximum element in an array.**
```
// val values = arrayOf(1,8,3,4,5,6)

fun maxElementInArray(values: Array<Int>) : Int{

    if (values == null || values.isEmpty())
        return -1

    var maxValue = values[0];
    for (i in values.indices){
        if (values[i] > maxValue){
            maxValue = values[i];
        }
    }
    return maxValue
}
```
**Create a program to remove duplicates from a list using Kotlin.**
```
// val list = listOf(1,1,2,2,3,3,4,4,5,5,6)

fun removeDuplicates(values: List<Int>) : List<Int> {

    val set = mutableSetOf<Int>()

    for (index in values.indices){
        set.add(values[index])
    }
    return set.toList()
}
```

**Write a Kotlin function to find the intersection of two arrays (common elements).**
```
// val values1 = arrayOf(1,2,3,4,5,6)
// val values2 = arrayOf(6,7,8,9,10,11,2)

fun getCommonElements(values1: Array<Int>, values2: Array<Int>) : List<Int> {
    val result = mutableListOf<Int>()

    for (index1 in values1.indices){
        for (index2 in values2.indices){
            if (values2[index2] == values1[index1])
                result.add(values2[index2])
        }
    }

    return result
}
```

**Implement a function that checks if a list is sorted in ascending order.**
```
fun isListAscending(values: List<Int>) : Boolean {
    if (values.isEmpty() || values.size == 1)
        return false

    for (index in 0 until values.size - 1){
        if (values[index] > values[index+1]){
            return false
        }
    }
    return true
}
```

**Create a Kotlin program to count the frequency of words in a text document.**
```
fun frequencyOfWords(text: String) : Map<String, Int> {
    val map = mutableMapOf<String, Int>()

    val tokens = text.split(" ")
    tokens.forEach { token -> 
        var value : Int = map.getOrDefault(token, 0)
        value++
        map[token] = value
    }

    return map
}
```

### Functions and Lambdas:

15. Write a Kotlin function that takes a string and returns the reverse of that string.
16. Implement a higher-order function that takes a list of numbers and a lambda to filter out even numbers.
17. Create a function that computes the nth Fibonacci number using recursion.
18. Write a program to find the common elements in two lists using a lambda function.
19. Implement a Kotlin program to calculate the sum of digits in a given number.
20. Create a function that generates a random password with a specified length.

