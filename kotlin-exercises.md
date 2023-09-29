

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

**Data Types and Variables:**

6. Create a Kotlin program to swap the values of two variables without using a temporary variable.
7. Write a program to check if a given number is prime or not using Kotlin.
8. Calculate the square root of a number using the `Math.sqrt` function.
9. Create a program that converts a decimal number to a binary representation in Kotlin.

**Collections:**

10. Write a Kotlin function to find the maximum element in an array.
11. Create a program to remove duplicates from a list using Kotlin.
12. Write a Kotlin function to find the intersection of two arrays (common elements).
13. Implement a function that checks if a list is sorted in ascending order.
14. Create a Kotlin program to count the frequency of words in a text document.

**Functions and Lambdas:**

15. Write a Kotlin function that takes a string and returns the reverse of that string.
16. Implement a higher-order function that takes a list of numbers and a lambda to filter out even numbers.
17. Create a function that computes the nth Fibonacci number using recursion.
18. Write a program to find the common elements in two lists using a lambda function.
19. Implement a Kotlin program to calculate the sum of digits in a given number.
20. Create a function that generates a random password with a specified length.

