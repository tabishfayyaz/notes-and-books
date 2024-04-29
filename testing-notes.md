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

### Why can't we use real dependencies in a Unit test

In unit testing, we aim to isolate the unit under test and verify its behavior independently of its dependencies. By using real dependencies, the unit test can become more of an integration test, as it may be testing the interaction between the unit and its dependencies rather than the unit itself.

Here are some reasons why we typically mock dependencies in unit tests:

1. **Isolation**: Unit tests should focus on testing a single unit of code in isolation. When we use real dependencies, the behavior of those dependencies can affect the outcome of the test, making it harder to isolate the unit under test.

2. **Controlled Environment**: With mocked dependencies, we have full control over their behavior, allowing us to simulate different scenarios and edge cases that may be difficult to reproduce with real dependencies.

3. **Speed**: Mocking dependencies can make tests run faster because we're not performing actual I/O operations or accessing external resources. Mock objects are lightweight and don't incur the overhead of setting up and tearing down real resources.

4. **Consistency**: Real dependencies may change over time, leading to test instability or false positives/negatives. Mocking allows us to maintain consistency in test behavior regardless of changes in the actual dependencies.

However, it's essential to strike a balance between using real dependencies and mocks. While unit tests should primarily use mocks to isolate units of code, integration tests are necessary to verify the interaction between real components in a more realistic environment.

In cases where it's crucial to test the integration between components, integration tests or end-to-end tests using real dependencies are more appropriate. These tests ensure that the system functions correctly as a whole, including its interactions with external components.

## References
- https://androidessence.com/test-driven-development
- Sample test example: https://gist.github.com/AdamMc331/c815f3ae7579409b01b0fbfd5c9984aa
- https://www.toptal.com/java/a-guide-to-everyday-mockito
- https://www.baeldung.com/mockito-spy
- https://www.baeldung.com/mockito-verify
- https://www.baeldung.com/mockito-series
- https://www.javadoc.io/doc/org.mockito/mockito-core/2.18.3/org/mockito/Mockito.html (seems to have good examples)
