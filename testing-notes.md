**TDD:** developers write tests prior to writing the code itself which would result in a bunch of failing tests that cover our expected behavior and will fail until required code is written to make them pass. 

**Behavioral Verification:** Verifies this method is called.

**State-based Verification:** Verifies this method is called with those specific attributes.

**Mock:** A generated class that doesn’t do anything, used for behavior verification.

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

## Resources
- https://androidessence.com/test-driven-development
- Sample test example: https://gist.github.com/AdamMc331/c815f3ae7579409b01b0fbfd5c9984aa
- https://www.toptal.com/java/a-guide-to-everyday-mockito
