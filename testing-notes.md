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

### Stub vs Mock Example
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

## References
- https://androidessence.com/test-driven-development
- Sample test example: https://gist.github.com/AdamMc331/c815f3ae7579409b01b0fbfd5c9984aa
- https://www.toptal.com/java/a-guide-to-everyday-mockito
- https://www.baeldung.com/mockito-spy
- https://www.baeldung.com/mockito-verify
- https://www.baeldung.com/mockito-series
- https://www.javadoc.io/doc/org.mockito/mockito-core/2.18.3/org/mockito/Mockito.html (seems to have good examples)
