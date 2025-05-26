# Unit Testing Cheat Sheet: Failure-Driven Discovery Approach

## The Philosophy: Let Failures Guide You

Instead of trying to mock everything upfront, let compilation errors and runtime failures tell you exactly what you need. This is faster and more accurate than guessing dependencies.

## Step-by-Step Process

### 1. Start with the Bare Minimum Test Structure

```kotlin
@Test
fun methodName_scenario_expectedBehavior() = runTest {
    // Arrange - Start empty, build as failures demand
    val viewModel = YourViewModel(
        // Leave constructor empty initially
    )
    
    // Act
    viewModel.yourMethod()
    
    // Assert - Figure out later
}
```

**Key Principle:** Don't mock anything until the compiler or runtime forces you to.

### 2. Fix Compilation Errors First

When the compiler complains about missing constructor parameters:

```kotlin
val viewModel = YourViewModel(
    param1 = mockk(),     // Add these one by one
    param2 = mockk(),     // as compiler demands
    param3 = mockk(),
)
```

**Pro Tip:** Use your IDE's auto-complete to see required parameters instead of guessing.

### 3. Handle Runtime Exceptions with Relaxed Mocks

When you get `MockKException` or similar runtime errors:

```kotlin
// Quick fix: Make ALL mocks relaxed initially
val viewModel = YourViewModel(
    param1 = mockk(relaxed = true),
    param2 = mockk(relaxed = true),
    param3 = mockk(relaxed = true),
)
```

**Why relaxed mocks:** They return sensible defaults (false for Boolean, 0 for Int, empty collections) and let your test run to reveal the actual behavior.

### 4. Debug Specific Mock Issues

When you get specific exceptions like `KotlinNothingValueException`:

#### For Flow/Collection Issues
```kotlin
// Problem: dependency.someFlow.collect { ... }
// Solution: Mock the Flow properly
val mockFlow = MutableSharedFlow<YourDataType>()
every { dependency.someFlow } returns mockFlow
```

#### For Method Chain Issues
```kotlin
// Problem: repository.getShoppingList().shoppingListCollectionInfo
// Solution: Mock the entire chain
val mockShoppingList = mockk<ShoppingList>(relaxed = true)
every { mockShoppingList.shoppingListCollectionInfo } returns mockFlow

val repository = mockk<Repository>(relaxed = true)
every { repository.getShoppingList(any()) } returns mockShoppingList
```

#### For Constructor/Init Block Issues
Remember: Exceptions during ViewModel creation happen in `init` blocks, not your test method. Set up all required mocks **before** creating the ViewModel.

### 5. Test Coroutines and Flow Emissions

For methods that emit to SharedFlow/StateFlow:

```kotlin
@Test
fun methodName_scenario_emitsExpectedValue() = runTest {
    // Arrange
    val viewModel = // ... set up as above
    
    val emissions = mutableListOf<YourEmissionType>()
    val job = launch {
        viewModel.yourPublicFlow.collect { 
            emissions.add(it) 
        }
    }
    
    // Act
    viewModel.yourMethod()
    advanceUntilIdle() // Let coroutines complete
    
    // Assert
    assertEquals(1, emissions.size)
    // Verify emission content...
    
    job.cancel()
}
```

**Key Points:**
- Use `runTest` for coroutine testing
- Collect emissions in a separate coroutine
- Use `advanceUntilIdle()` to let coroutines complete
- Always cancel collection jobs

## Real Example Walkthrough

**Method to test:** ViewModel method that launches coroutine and emits `Event<AddToCart>` to a SharedFlow.

### Step 1: Start Simple
```kotlin
@Test
fun addAllToCart_triggersCorrectEvent() = runTest {
    val viewModel = YourViewModel() // ❌ Compiler error - missing params
}
```

### Step 2: Fix Constructor
```kotlin
val viewModel = YourViewModel(
    param1 = mockk(),
    param2 = mockk(), // ❌ Runtime error - MockKException
)
```

### Step 3: Make Mocks Relaxed
```kotlin
val viewModel = YourViewModel(
    param1 = mockk(relaxed = true),
    param2 = mockk(relaxed = true), // ❌ Still failing - KotlinNothingValueException
)
```

### Step 4: Debug Specific Issue
**Error:** `KotlinNothingValueException` on line: `shoppingListRepository.getShoppingList().shoppingListCollectionInfo.collect`

**Analysis:** The ViewModel init block is trying to collect from a Flow, but our mock doesn't provide a real Flow.

**Solution:**
```kotlin
val emptySharedFlow = MutableSharedFlow<Resource<ShoppingListCollectionInfo>>()

val mockShoppingList = mockk<ShoppingList>(relaxed = true)
every { mockShoppingList.shoppingListCollectionInfo } returns emptySharedFlow

val shoppingListRepository = mockk<ShoppingListRepository>(relaxed = true)
every { shoppingListRepository.getShoppingList(any()) } returns mockShoppingList
```

### Step 5: Complete the Test
```kotlin
@Test
fun addAllToCart_triggersCorrectEvent() = runTest {
    // Arrange
    val emptySharedFlow = MutableSharedFlow<Resource<ShoppingListCollectionInfo>>()
    val mockShoppingList = mockk<ShoppingList>(relaxed = true)
    every { mockShoppingList.shoppingListCollectionInfo } returns emptySharedFlow
    
    val shoppingListRepository = mockk<ShoppingListRepository>(relaxed = true)
    every { shoppingListRepository.getShoppingList(any()) } returns mockShoppingList
    
    val viewModel = YourViewModel(
        param1 = mockk(relaxed = true),
        param2 = shoppingListRepository,
    )
    
    val emissions = mutableListOf<Event<AddToCart>>()
    val job = launch {
        viewModel.addToCartEvents.collect { emissions.add(it) }
    }
    
    // Act
    viewModel.addAllToCart()
    advanceUntilIdle()
    
    // Assert
    assertEquals(1, emissions.size)
    emissions.first().process { addToCart ->
        assertEquals(ButtonChoice.ADD_ALL_TO_CART, addToCart.buttonChoice)
        assertEquals(Method.DROPOFF, addToCart.method)
    }
    
    job.cancel()
}
```

## Common Patterns & Solutions

### Mock Method with Parameters
```kotlin
// Use any() for quick discovery
every { repository.getShoppingList(any()) } returns mockResult

// Use specific values when needed
every { repository.getShoppingList("specific-id") } returns mockResult

// Capture actual parameters for verification
val slot = slot<String>()
every { repository.getShoppingList(capture(slot)) } returns mockResult
// Later: assertEquals("expected-id", slot.captured)
```

### Mock Flows
```kotlin
// Empty flow (won't emit anything)
val emptyFlow = MutableSharedFlow<DataType>()

// Flow with test data
val testFlow = MutableSharedFlow<DataType>()
// Later: testFlow.emit(testData)

// Simple single emission
every { dependency.someFlow } returns flowOf(testData)
```

### Debug Mock Setup
```kotlin
// Test your mock chain before using it
val testResult = repository.getShoppingList("test")
println("Returned: $testResult")
println("Flow: ${testResult.someFlow}")
```

### Handle Different Return Types
```kotlin
// For suspend functions
coEvery { repository.fetchData() } returns testData

// For functions returning nullable types
every { repository.getData() } returns testData // or null

// For functions with Unit return
every { repository.doSomething() } just Runs
```

## Quick Debugging Checklist

When your test fails:

1. **Compilation Error?** → Add missing constructor parameters with `mockk()`
2. **MockKException?** → Make mocks `relaxed = true`
3. **KotlinNothingValueException?** → Look for Flow/collection issues, mock the Flow properly
4. **Wrong mock variable?** → Double-check you're passing the right mock instances
5. **Init block issues?** → Set up all mocks before ViewModel creation
6. **Coroutine not completing?** → Add `advanceUntilIdle()`
7. **No emissions collected?** → Check if your method actually emits, verify timing

## Speed Tips

- **Copy-paste-modify:** Once you have one working test, duplicate it for other scenarios
- **Start with relaxed mocks:** Tighten them later if you need to verify specific interactions
- **Use `any()` liberally:** Perfect for discovery phase, make specific later if needed
- **Test one path at a time:** Don't try to handle all edge cases in your first test
- **Let failures guide you:** Trust the process - failures reveal exactly what you need

## The Result

This approach gives you:
- **Faster test writing** - No upfront guessing about dependencies
- **Minimal mocking** - Only mock what's actually used
- **Accurate tests** - Based on real behavior, not assumptions
- **Easy maintenance** - Clear relationship between test failures and required mocks

Remember: The goal is working tests quickly, not perfect mocks upfront. Let the failures teach you what your code actually needs!

# Questions & Answers

### Why can't we use real dependencies in a Unit test

In unit testing, we aim to isolate the unit under test and verify its behavior independently of its dependencies. By using real dependencies, the unit test can become more of an integration test, as it may be testing the interaction between the unit and its dependencies rather than the unit itself.

Here are some reasons why we typically mock dependencies in unit tests:

1. **Isolation**: Unit tests should focus on testing a single unit of code in isolation. When we use real dependencies, the behavior of those dependencies can affect the outcome of the test, making it harder to isolate the unit under test.

2. **Controlled Environment**: With mocked dependencies, we have full control over their behavior, allowing us to simulate different scenarios and edge cases that may be difficult to reproduce with real dependencies.

3. **Speed**: Mocking dependencies can make tests run faster because we're not performing actual I/O operations or accessing external resources. Mock objects are lightweight and don't incur the overhead of setting up and tearing down real resources.

4. **Consistency**: Real dependencies may change over time, leading to test instability or false positives/negatives. Mocking allows us to maintain consistency in test behavior regardless of changes in the actual dependencies.

However, it's essential to strike a balance between using real dependencies and mocks. While unit tests should primarily use mocks to isolate units of code, integration tests are necessary to verify the interaction between real components in a more realistic environment.

In cases where it's crucial to test the integration between components, integration tests or end-to-end tests using real dependencies are more appropriate. These tests ensure that the system functions correctly as a whole, including its interactions with external components.

### What is difference between mock and stub

Consider a scenario where we have a `Calculator` interface with an `add` method, and we want to test a `CalculatorService` class that depends on this interface.

1. **Stub Example**:

Suppose we have a stub implementation of the `Calculator` interface:

```kotlin
interface Calculator {
    fun add(a: Int, b: Int): Int
}

class CalculatorStub : Calculator {
    override fun add(a: Int, b: Int): Int {
        // Stub implementation that always returns a fixed value
        return 5
    }
}
```

In this case, `CalculatorStub` is a stub implementation of the `Calculator` interface. It provides a fixed implementation of the `add` method that always returns `5`. We can use this stub to provide predetermined responses during testing, regardless of the input parameters.

2. **Mock Example**:

Now, let's consider a mock object for the `Calculator` interface:

```kotlin
import org.mockito.Mockito.*

val calculatorMock = mock(Calculator::class.java)

`when`(calculatorMock.add(2, 3)).thenReturn(5)
```

In this example, `calculatorMock` is a mock object created using a mocking framework like Mockito. We use `when(calculatorMock.add(2, 3)).thenReturn(5)` to specify the behavior of the mock object. This configuration tells Mockito that when the `add` method of the mock object is called with parameters `2` and `3`, it should return `5`. Unlike a stub, a mock allows us to verify interactions, such as method calls, during testing.

In summary:

- **Stub**: A stub provides predetermined responses to method calls and is used to simulate the behavior of real objects in a controlled manner. Stubs are primarily used to provide input to the unit under test and are not concerned with verifying interactions.

- **Mock**: A mock object records method calls and their parameters during test execution and allows us to verify interactions between the unit under test and its dependencies. Mocks are used to specify expectations about how the unit under test should interact with its dependencies and to verify these expectations after test execution.

### What and how many types of test doubles are there

Test doubles are objects or components used in unit testing to replace real dependencies and facilitate isolated testing. There are several types of test doubles commonly used in unit testing:

1. **Dummy**: Dummy objects are placeholders that are passed around but never actually used. They are used when a parameter is required by the method being tested but the actual value is irrelevant to the test.

2. **Stub**: Stubs provide canned responses to method calls made during the test. They are used to simulate the behavior of real objects, providing predefined responses to specific method calls.

3. **Fake**: Fakes are simplified implementations of dependencies that behave similarly to the real object but are optimized for testing purposes. They typically have limited functionality compared to the real implementation.

4. **Mock**: Mocks are objects with pre-programmed expectations about the behavior of the code being tested. They record interactions with the mocked object and allow assertions to be made about those interactions.

5. **Spy**: Spies are real objects that have been instrumented to record their interactions with the code being tested. They allow you to verify that certain methods are called or to inspect the behavior of the object during the test.

6. **Double**: This is a generic term used to refer to any kind of test double.

These types of test doubles are used in various combinations to isolate the code under test from its dependencies and to verify its behavior in isolation. Each type of test double serves a specific purpose and can be used to test different aspects of the code being tested.

# Notes

**TDD:** developers write tests prior to writing the code itself which would result in a bunch of failing tests that cover our expected behavior and will fail until required code is written to make them pass. 

**Behavioral Verification:** Verifies this method is called.

**State-based Verification:** Verifies this method is called with those specific attributes.

**Mock:** A generated class that doesn’t do anything, used for behavior verification. Mocks are used as a replacement for a dependency

**Stub:** A hand-written class that returns a pre-defined response given certain input, used for state verification.

### Testing Steps
**Given:** initial conditions, create instances

**When:** the actions we want to trigger, the thing to be tested

**Then:** did it work or not

### Thought process for Developing Test Plan 
- What should we do if no object instance is supplied?
- What should we do if we’re supplied an object instance with no required info?
- What should we do if we’re supplied an object instance with only one required info?
- What should we do if we’re supplied an object instance with two required info?
- What should we do if we’re supplied an object instance with more than two required info?

In order to **enable Mockito annotation** (such as *@Spy, @Mock,* … ) - we need to do one of the following:

- Call the method *MockitoAnnotations.initMocks(this)* to initialize annotated fields
- Use the built-in runner *@RunWith(MockitoJUnitRunner.class)*

![alt text](https://github.com/tabishfayyaz/book-club/raw/master/images/mockvsspy.png "mock vs spy")

- *Mock* is a bare double object. This object has the same methods signatures but realisation is empty and return default value - 0 and null
- *Spy* is a cloned double object. New object is cloned based on a real object but you have a possibility to mock it
- If there is an object with 8 methods and you have a test where you want to call 7 real methods and stub one method you have two options:
  1. Using a mock you would have to set it up by invoking 7 *callRealMethod* and stub one method
  2. Using a spy you have to set it up by stubbing one method

### Sample Test Class:

```
@RunWith(MockitoJUnitRunner::class)
class PlayerProviderTest {

}
```

### Stub vs Mock JUnit Example
Let's illustrate the difference between a stub and a mock in JUnit using a simple example. In this example, we'll create a `UserService` class that depends on a `UserRepository` to retrieve user data. We'll use JUnit along with the Mockito library to demonstrate stubbing and mocking.

Assume that our `UserRepository` interface and `UserService` class look like this:

```java
public interface UserRepository {
    User getUserById(int userId);
}

public class UserService {
    private UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public String getUsernameById(int userId) {
        User user = userRepository.getUserById(userId);
        return user != null ? user.getUsername() : "User not found";
    }
}
```

Now, let's create tests for the `UserService` class using JUnit and Mockito:

1. **Stub Example**:

In this example, we'll use a stub to provide predefined responses from the `UserRepository`:

```java
import org.junit.Test;
import static org.junit.Assert.assertEquals;
import static org.mockito.Mockito.mock;
import static org.mockito.Mockito.when;

public class UserServiceTest {

    @Test
    public void testGetUsernameByIdWithStub() {
        // Create a UserRepository stub
        UserRepository userRepositoryStub = mock(UserRepository.class);

        // Define stub behavior
        when(userRepositoryStub.getUserById(1)).thenReturn(new User(1, "stubUser"));

        // Create the UserService with the stub
        UserService userService = new UserService(userRepositoryStub);

        // Test the getUsernameById method
        String username = userService.getUsernameById(1);

        // Assert that the result matches the stubbed value
        assertEquals("stubUser", username);
    }
}
```

In this test, we use `when` and `thenReturn` from Mockito to specify that when `getUserById(1)` is called on the `UserRepository` stub, it should return a predefined `User` object with the username "stubUser."

2. **Mock Example**:

In this example, we'll use a mock to verify interactions with the `UserRepository`:

```java
import org.junit.Test;
import static org.mockito.Mockito.mock;
import static org.mockito.Mockito.verify;
import static org.mockito.Mockito.when;

public class UserServiceTest {

    @Test
    public void testGetUsernameByIdWithMock() {
        // Create a UserRepository mock
        UserRepository userRepositoryMock = mock(UserRepository.class);

        // Define mock behavior
        when(userRepositoryMock.getUserById(1)).thenReturn(new User(1, "mockUser"));

        // Create the UserService with the mock
        UserService userService = new UserService(userRepositoryMock);

        // Test the getUsernameById method
        String username = userService.getUsernameById(1);

        // Verify that getUserById(1) was called on the mock
        verify(userRepositoryMock).getUserById(1);

        // Assert that the result matches the mock response
        assertEquals("mockUser", username);
    }
}
```

In this test, we use `verify` from Mockito to check that the `getUserById(1)` method was called on the `UserRepository` mock, and we also specify that it should return a predefined `User` object.

In summary, the stub is used to provide predefined responses for method calls, while the mock is used to verify interactions with the dependency. Both stubbing and mocking are useful techniques in testing, depending on the specific testing goals and scenarios.

### Testing with Dependency Injection (Dagger) in Java using Mock and Real dependencies
In this example, we will create a Java application with Dagger for dependency injection and then demonstrate testing with both real and mock dependencies. We'll create a `UserService` class that depends on a `UserRepository` for fetching user data.

Here are the key classes:

1. **UserRepository.java**: An interface for fetching user data.

```java
public interface UserRepository {
    User getUserById(int userId);
}
```

2. **User.java**: A simple User class.

```java
public class User {
    private int id;
    private String username;

    public User(int id, String username) {
        this.id = id;
        this.username = username;
    }

    public String getUsername() {
        return username;
    }
}
```

3. **UserModule.java**: A Dagger module that provides a real implementation of `UserRepository`.

```java
@Module
public class UserModule {
    @Provides
    UserRepository provideUserRepository() {
        return new RealUserRepository();
    }
}
```

4. **RealUserRepository.java**: A real implementation of `UserRepository`.

```java
public class RealUserRepository implements UserRepository {
    @Override
    public User getUserById(int userId) {
        // Fetch user data from a database or external source (not shown in this example)
        return new User(1, "realUser");
    }
}
```

5. **UserService.java**: A class that depends on `UserRepository` for fetching user data.

```java
public class UserService {
    private UserRepository userRepository;

    @Inject
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public String getUsernameById(int userId) {
        User user = userRepository.getUserById(userId);
        return user != null ? user.getUsername() : "User not found";
    }
}
```

Now, let's create tests for the `UserService` class using Dagger for dependency injection with both real and mock dependencies:

6. **UserServiceTest.java**: A test class for `UserService` that uses Dagger for dependency injection with real and mock dependencies.

```java
@RunWith(MockitoJUnitRunner.class)
public class UserServiceTest {

    @Inject
    UserService userService;

    @Before
    public void setup() {
        // Initialize Dagger components/modules for testing with real dependencies
        DaggerUserServiceTestComponent.builder()
                .userModule(new UserModule())
                .build()
                .inject(this);
    }

    @Test
    public void testGetUsernameByIdWithRealDependency() {
        String username = userService.getUsernameById(1);
        assertEquals("realUser", username);
    }

    @Test
    public void testGetUsernameByIdWithMockDependency() {
        // Create a mock UserRepository for testing
        UserRepository mockRepository = mock(UserRepository.class);
        when(mockRepository.getUserById(1)).thenReturn(new User(1, "mockUser"));

        // Inject the mock dependency using Dagger
        DaggerUserServiceTestComponent.builder()
                .userModule(new UserModule() {
                    @Override
                    UserRepository provideUserRepository() {
                        return mockRepository;
                    }
                })
                .build()
                .inject(this);

        String username = userService.getUsernameById(1);
        assertEquals("mockUser", username);
    }

    @Component(modules = {UserModule.class})
    interface UserServiceTestComponent {
        void inject(UserServiceTest test);
    }
}
```

In this test class:

- We use the `@RunWith(MockitoJUnitRunner.class)` annotation to run the test with Mockito.

- In the `setup()` method, we initialize Dagger components and modules for testing with real dependencies and inject dependencies into the test class.

- In `testGetUsernameByIdWithRealDependency()`, we test the `UserService` with real dependencies. We expect the result to be "realUser."

- In `testGetUsernameByIdWithMockDependency()`, we create a mock `UserRepository` and inject it into the `UserService` using Dagger. We then test the `UserService` with the mock dependency. We expect the result to be "mockUser."

- The `UserServiceTestComponent` interface defines the Dagger component for testing and includes the `UserModule` to provide dependencies. For the second test, we override the `provideUserRepository()` method in the module to provide the mock dependency.

By using Dagger for dependency injection in testing, we can easily switch between real and mock dependencies, allowing us to test the `UserService` with different scenarios and ensure its behavior is correct.

## References
- https://github.com/HugoMatilla/Unit-Testing-Cheat-Sheet
- https://androidessence.com/test-driven-development
- Sample test example: https://gist.github.com/AdamMc331/c815f3ae7579409b01b0fbfd5c9984aa
- https://www.toptal.com/java/a-guide-to-everyday-mockito
- https://www.baeldung.com/mockito-spy
- https://www.baeldung.com/mockito-verify
- https://www.baeldung.com/mockito-series
- https://www.javadoc.io/doc/org.mockito/mockito-core/2.18.3/org/mockito/Mockito.html (seems to have good examples)
