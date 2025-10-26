(test-logging)=
# Test Logging

(Currently only hinted at in the example - not used everywhere and fully)

When testing with PyTest, we have a choice of different approaches to logging:
- **Caplog** - Pytest functionality (probably the best path when you want to do something with logs, offering added functionality to standard Python logging)
- **Standard Python logging** (good path. Caplog is naturally built on it and uses it for the actual logging action)
- **Write your own logging** (well, why struggle with it. Even writing prints is unnecessarily complicated)
- **Use another external logging library** (Worth considering, but to be advantageous it must have a very weighted benefit)

From this logically follows: use the Python logging path in combination with Caplog in the course. More about this topic can be found here:
- https://pytest-with-eric.com/fixtures/built-in/pytest-caplog/
- https://pytest-with-eric.com/pytest-best-practices/pytest-logging/#Custom-Logger-vs-Inbuilt-Logging-Module

And also some other topics we've already covered in the course.

There you'll also find a very good theoretical description with practical examples and explanation of why to use caplog:
- **Can separate pytest phases** (setup/teardown/test execution...)
- **Can separate logs from different tests** (has pytest context)
- **You can easily use it for conditions and asserting** whether something was/wasn't logged
- ...

`test.py`
```python
...
import logging
...

@pytest.fixture()
def login_page():
    login_page = "https://www.saucedemo.com/"
    logging.debug(f"Going to login page '{login_page}'")
    browser.get(login_page)
    logging.info(f"Login page '{login_page}' loaded already.")
    yield
    current_page = browser.current_url
    try:
        logging.debug(f"Going make user logoff via page menu from page '{current_page}'")
        menu = Menu(browser)
        menu.logout()
        logging.info(f"Logoff form '{current_page}' already done.")
    except:
        logging.warn(f"The page '{current_page}' was not logged in!")
```

Notice these tricks here:
- **Different logging levels are used**:
    - `debug` - when we'll be looking for how it behaves exactly and in detail
    - `info` - basic logging that informs generally about something happening
    - `warn` - when something is highly suspicious but it's not yet clear if it's a problem
    - `error` - not used in this example, but quite often part of except code sections. We can clearly determine it's erroneous behavior

- **Parameters and states** like `login_page`, `current_page` enter the logging, precisely so that logging tells us something real
- **f-string convention is used** with proper variable naming so it's beautifully readable from the code and actually used instead of comments. But these comments also appear in test results (so it has a dual purpose)

```python
f"Login page '{login_page}' loaded already."
```

- **It's specifically put in quotes** so from the resulting logs it's exactly clear where the given string/value begins and ends, and you could detect an extra space or variable emptiness just by looking at the logs

```python
'{login_page}'
```

## Live Logging - How and Why to Simply Display Logs

If you want to display log output in the console, just add the parameter `--log-cli-level [log_level]`

```bash
pytest .\test.py --log-cli-level INFO
```
```bash
pytest .\test.py --log-cli-level DEBUG
```
```bash
pytest .\test.py --log-cli-level 0
```

More about levels and live logging can be found here:
- https://docs.python.org/3/library/logging.html#levels
- https://docs.pytest.org/en/7.1.x/how-to/logging.html#live-logs

It also explains how to send logs through pytest to a file.

## Why is it good to pay attention to logs from your test and look at them?

- **You'll realize what the test flow looks like** for others, and whether something else needs to be printed/fixed for it to be correct or understandable
- **You'll reveal potential errors** you haven't noticed so far (Maybe it doesn't go through some part of the code at all, or the tested subject - System Under Test - SUT behaves differently than you'd expect)
- **Logs can be used to verify** that the test works as it should
- ...

## Advanced Logging Strategies

### Structured Logging with Context:
```python
import logging

logger = logging.getLogger(__name__)

def test_login_flow(self, username, password):
    logger.info(f"Starting login test", extra={
        'username': username,
        'test_phase': 'setup',
        'page_url': browser.current_url
    })
    
    # Test logic here
    
    logger.info(f"Login test completed", extra={
        'result': 'success',
        'test_phase': 'teardown'
    })
```

### Using Caplog for Assertions:
```python
def test_login_generates_correct_logs(self, caplog):
    with caplog.at_level(logging.INFO):
        # Perform login action
        login_page.login("user", "pass")
        
        # Assert specific logs were generated
        assert "Login attempt for user" in caplog.text
        assert len(caplog.records) >= 2
        assert caplog.records[0].levelname == "INFO"
```

### Log Level Guidelines:

#### DEBUG Level:
- Detailed step-by-step execution
- Variable values and state changes
- Entry/exit of functions and methods
- Selenium action details

#### INFO Level:  
- Major test milestones
- Successful operations
- Test phase transitions (setup/test/teardown)
- Business logic outcomes

#### WARNING Level:
- Unexpected but handleable situations  
- Fallback behaviors triggered
- Performance issues detected
- Configuration problems

#### ERROR Level:
- Test failures and exceptions
- System integration problems
- Critical functionality failures
- Infrastructure issues

### Best Practices:

#### Variable Logging:
```python
# Good: Clear boundaries and context
logging.info(f"Navigating to page '{target_url}' from '{current_url}'")

# Better: Include relevant context
logging.debug(f"Element located with strategy '{locator_type}' and value '{locator_value}' - found: {element_found}")
```

#### Exception Logging:
```python
try:
    menu.logout()
    logging.info(f"Successfully logged out from '{current_page}'")
except Exception as e:
    logging.error(f"Logout failed from '{current_page}': {str(e)}")
    raise  # Re-raise to maintain test failure
```

```{tip}
**Log for Future You**: Write logs that will help you debug issues months later when you've forgotten the test details. Include enough context to understand what the test was trying to accomplish.
```

```{seealso}
Effective logging supports {ref}`Test Design Patterns <test-design-patterns>` by providing insight into test flow and helps debug issues with {ref}`Data-Driven Testing <data-driven-testing>` scenarios.
```