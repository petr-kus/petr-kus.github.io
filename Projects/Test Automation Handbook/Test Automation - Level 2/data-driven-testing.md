(data-driven-testing)=
# Data-Driven Testing

**Data-Driven Test** - consumes data from a table (can include expected results, not just input parameters). It repeatedly tries the written scenario for all values from the given table. Adding another test case involves only adding another row with input and expected output data to the table.

This is another powerful feature of Pytest (or any other good test framework).

`test.py`:
```python
class TestWebPage:

    ...

    @pytest.mark.parametrize("loginame, password", 
                            [("standard_user", "secret_sauce"), 
                            ("problem_user", "secret_sauce"), 
                            ("performance_glitch_user", "secret_sauce"), 
                            ("error_user", "secret_sauce"), 
                            ("visual_user", "secret_sauce")])
    def test_Successful_Login_and_Logout(self, loginame, password):
        """ This is testing successful login and logout to the page"""
        ...
        browser.get(self.test_page)
        ...
        loginPage.login(loginame, password)
        assert "inventory" in browser.current_url
        menu.logout()
        assert self.test_page == browser.current_url
        ...
```

Notice that the login scenario above includes navigating to the page, logging in, and logging out ('setup'/'teardown').

## Advantages this brings:

- **Entry and exit point/state** of the test allows quick execution of all combinations in sequence.
- **The test is also isolated/atomic** - it doesn't care what ran before it or after it.
- **I can run it in any order** relative to other tests.
- **It doesn't matter how I rearrange the rows** in the table.

## Advanced Parametrization Patterns

### Multiple Parameters with Expected Results:
```python
@pytest.mark.parametrize("username, password, expected_url, should_succeed", [
    ("standard_user", "secret_sauce", "inventory.html", True),
    ("locked_out_user", "secret_sauce", "saucedemo.com", False),
    ("invalid_user", "wrong_password", "saucedemo.com", False),
])
def test_login_scenarios(self, username, password, expected_url, should_succeed):
    # Test logic using expected results
    ...
```

### Using Pytest IDs for Better Test Names:
```python
@pytest.mark.parametrize("username, password", [
    pytest.param("standard_user", "secret_sauce", id="standard_user"),
    pytest.param("problem_user", "secret_sauce", id="problem_user"),
    pytest.param("locked_out_user", "secret_sauce", id="locked_user"),
])
```

### External Data Sources:
```python
# Load test data from JSON, CSV, or database
test_data = load_test_data_from_file("login_test_data.json")

@pytest.mark.parametrize("test_case", test_data)
def test_login_from_external_data(self, test_case):
    # Use test_case dictionary for test data
    ...
```

## Test Design Benefits

### Scalability:
- **Easy test case addition**: Just add new rows to the parameter list
- **Data maintenance**: Test logic stays constant, only data changes
- **Coverage expansion**: Quickly test edge cases and variations

### Maintainability:
- **Single test logic**: One test method handles all variations
- **Clear separation**: Test logic separate from test data
- **Debugging efficiency**: Each parameter set runs as individual test

### Isolation:
- **Independent execution**: Each parameter set is a separate test instance
- **No state leakage**: Tests don't affect each other
- **Parallel execution**: Parameter sets can run in parallel

```{warning}
**Atomic Test Design**: Make sure each parametrized test includes proper setup and teardown. Don't rely on previous test runs to set up state for subsequent tests.
```

```{seealso}
Data-driven testing works well with {ref}`Centralized Configuration <centralized-config>` for managing test data and {ref}`Test Design Patterns <test-design-patterns>` for comprehensive verification.
```