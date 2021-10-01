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

## References
- https://androidessence.com/test-driven-development
- Sample test example: https://gist.github.com/AdamMc331/c815f3ae7579409b01b0fbfd5c9984aa
- https://www.toptal.com/java/a-guide-to-everyday-mockito
- https://www.baeldung.com/mockito-spy
- https://www.baeldung.com/mockito-verify
- https://www.javadoc.io/doc/org.mockito/mockito-core/2.18.3/org/mockito/Mockito.html (seems to have good examples)
