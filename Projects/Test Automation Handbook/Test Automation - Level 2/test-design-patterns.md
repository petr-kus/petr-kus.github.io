(test-design-patterns)=
# Test Design for Login/Logout Verification

Multiple assertions are used in sequence without any problem, even for different things. Cross-verification of something in several ways.

```python
    def test_Successful_Login_and_Logout(self, loginame, password):
        """ This is testing successful login and logout to the page"""
        ...

        browser.get(self.test_page)
        loginPage.login(loginame, password)
        assert "inventory" in browser.current_url
        menu.logout()
        assert self.test_page == browser.current_url

        # After logout, try to navigate to the inventory page
        browser.get(self.test_page_inventory)
        # And verify that it redirects to the login page and doesn't reach inventory
        assert self.test_page == browser.current_url

        # And verify that some login error is displayed
        assert browser.find_element(*self.login_error_box)
```

In automated tests, really only what you write there is often verified.
- **So don't forget to write it!**
- **And try that it fails!**

P.S.: Sometimes it also surprises you that it caught something you didn't write there :-) - I call this verification through behavior/flow - behavior verification/behavior assertion.

## Multi-Layer Verification Strategy

### Why Multiple Assertions Work Well:

1. **Comprehensive Coverage**: Each assertion validates a different aspect of the functionality
2. **Fast Feedback**: All validations happen in one test run
3. **Cross-Verification**: Different ways of checking the same logical outcome
4. **Behavior Testing**: Some checks validate implicit behavior, not just explicit state

### Types of Verification in the Example:

#### Direct State Verification:
```python
assert "inventory" in browser.current_url  # Direct URL check after login
assert self.test_page == browser.current_url  # Direct URL check after logout
```

#### Behavioral Verification:
```python
# After logout, try to access protected page
browser.get(self.test_page_inventory)
# Verify it redirects back (behavior verification)
assert self.test_page == browser.current_url
```

#### UI State Verification:
```python
# Verify error message appears (UI feedback verification)
assert browser.find_element(*self.login_error_box)
```

## Best Practices for Test Design

### Make Tests Fail First:
Always verify that your assertions actually catch problems:
- Temporarily break the functionality being tested
- Run the test to confirm it fails
- Fix the functionality and confirm test passes

### Assertion Strategy Guidelines:

#### Use Multiple Assertions When:
- Testing complex workflows with multiple checkpoints
- Validating both positive and negative scenarios
- Cross-verifying the same logical outcome through different indicators

#### Avoid Multiple Assertions When:
- Each assertion tests completely unrelated functionality
- Failure of one assertion makes others meaningless
- Tests become difficult to understand or maintain

### Design for Clear Failure Messages:
```python
# Good: Clear, specific assertions
assert "inventory" in browser.current_url, f"Expected inventory page, got: {browser.current_url}"

# Better: Custom assertion messages
assert browser.find_element(*self.login_error_box), "Login error message should be displayed after logout"
```

## Flow-Based Testing

### Behavior Verification/Assertion:
Sometimes tests catch issues you didn't explicitly write assertions for, through the natural flow of the test:

- **Navigation behavior**: Redirects, URL changes, page transitions
- **State persistence**: Session handling, authentication state
- **Error handling**: How system responds to edge cases
- **Performance implications**: Timeouts, loading behavior

### Implicit vs Explicit Testing:
- **Explicit**: Direct assertions you write (`assert "inventory" in url`)
- **Implicit**: Behavior caught through test flow (page loads, elements exist, navigation works)

Both are valuable and complement each other in comprehensive test design.

```{tip}
**Test the Happy Path and Edge Cases**: Your comprehensive verification should cover both the expected successful flow and what happens when things go wrong (like trying to access protected pages after logout).
```

```{seealso}
This verification strategy works especially well with {ref}`Data-Driven Testing <data-driven-testing>` to test multiple scenarios and {ref}`Test Logging <test-logging>` to understand test flow and behavior.
```