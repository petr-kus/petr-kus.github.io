# Test Automation (Level 2)

This example demonstrates how test automation is typically written when you're at approximately Test Automation Level 2. It's a showcase that doesn't cover extensive page functionality, but rather demonstrates the direction and provides commentary on the test automation techniques and patterns used.

The example uses the test website saucedemo.com as the test project.

## What is Generally Used

- **Page Object Model** principle
- **Pytest** ("script" Test Framework)

## Project Structure

```
Test Folder/
├── PageObjects/
│   ├── Browser.py
│   ├── LoginPage.py
│   └── Menu.py
├── phtest.py
└── test.py
```
### Description of content
````{tab-set}
```{tab-item} test.py
:sync: test-py

Main test file containing test scenarios and parametrized test cases.
```

```{tab-item} Browser.py
:sync: browser-py

Browser setup and configuration utilities.
```

```{tab-item} LoginPage.py
:sync: loginpage-py

Page Object Model implementation for the login page functionality.
```

```{tab-item} Menu.py
:sync: menu-py

Page Object Model implementation for navigation menu interactions.
```
````
### Code Content
````{tab-set}
```{tab-item} test.py
:sync: test-py

```python
from selenium import webdriver
from selenium.webdriver.common.by import By
import time 
import pytest

#standartni python logging
import logging

#LOAD PAGE OBJECTS
from PageObjects.LoginPage import LoginPage
from PageObjects.Menu import Menu
#from PageObjects.Browser import Browser

def slowdown():
    sleep_time = 0.5
    if sleep_time > 0:
        logging.debug(f"slowdown and sleep {sleep_time}s to be visible during test development")
        time.sleep(sleep_time)

#OLD FIXTURES
"""
@pytest.fixture
def webpage():
    return "https://www.saucedemo.com/"

@pytest.fixture
def credentials():
    return {"password"   : "secret_sauce", "login_name" : "standart_user"}
"""

# Used constrution wit global variable and autouse=True 
# Why? -  I don't have to pass it now as fixture/object to each test...
@pytest.fixture(autouse=True, scope="session")
def Browser():
    global browser
    logging.debug(f"Starting browser chrome...")
    browser = webdriver.Chrome()
    logging.info(f"Browser started")
    yield browser
    logging.debug(f"Ending browser chrome...")
    browser.close()
    browser.quit()
    logging.info(f"Browser closed")

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
        logging.info(f"Logoff from '{current_page}' already done.")
    except:
        logging.warn(f"The page '{current_page}' was not logged in!")

class TestWebPage:

    test_page = "https://www.saucedemo.com/"
    test_page_inventory = "https://www.saucedemo.com/inventory.html"
    login_error_box = (By.XPATH,"//h3[@data-test='error']")

    @pytest.mark.parametrize("loginame, password", 
                            [("standard_user", "secret_sauce"), 
                            ("problem_user", "secret_sauce"), 
                            ("performance_glitch_user", "secret_sauce"), 
                            ("error_user", "secret_sauce"), 
                            ("visual_user", "secret_sauce")])
    def test_Successful_Login_and_Logout(self, loginame, password):
        """ This is testing successful login and logout to the page"""

        loginPage = LoginPage(browser)
        menu = Menu(browser)

        #DIFFERENT SOLUTION - possible add drver also to Page Object Model
        #browser = Browser(driver)
        #browser.go_to_page(test_page)

        browser.get(self.test_page)
        slowdown()
        loginPage.login(loginame, password)
        slowdown()
        assert "inventory" in browser.current_url
        slowdown()
        menu.logout()
        assert self.test_page == browser.current_url
        browser.get(self.test_page_inventory)
        assert self.test_page == browser.current_url
        assert browser.find_element(*self.login_error_box)

        #TODO: passing also for performance glitch user - shoudl not - have to be added verifictaion for performance

    @pytest.mark.parametrize("loginame, password", [("locked_out_user", "secret_sauce")])
    def test_Unsuccessful_Login(self, loginame, password):
        """ This is testing unsuccessful login to the page"""

        loginPage = LoginPage(browser)
        browser.get(self.test_page)
        slowdown()
        loginPage.login(loginame, password)
        slowdown()
        assert "inventory" not in browser.current_url
        assert browser.find_element(*self.login_error_box)

    #Approach with usage fixture nad yield for teardown (logout)
    @pytest.mark.parametrize("loginame, password", 
                            [("standard_user", "secret_sauce"), 
                            ("problem_user", "secret_sauce"), 
                            ("performance_glitch_user", "secret_sauce"), 
                            ("error_user", "secret_sauce"), 
                            ("visual_user", "secret_sauce")])
    def test_Successful_Login(self, login_page, loginame, password):
        """ This is testing successful login to the page"""
        loginPage = LoginPage(browser)
        slowdown()
        loginPage.login(loginame, password)
        slowdown()
        assert "inventory" in browser.current_url
```
```

```{tab-item} Browser.py
:sync: browser-py
```python
class Browser:
    def __init__(self,driver):
        self.driver = driver

    def go_to_page(self,page):
        self.driver.get(page)
    
    def page(self):
        return self.driver.current_url
```
```

```{tab-item} LoginPage.py
:sync: loginpage-py

Page Object Model implementation for the login page functionality.
```

```{tab-item} Menu.py
:sync: menu-py

Page Object Model implementation for navigation menu interactions.
```
````

In the example test code in this folder, pay special attention to the following constructs.

## POM - Page Object Model

In the test file (test.py), you'll find these related lines:
```python
...
from PageObjects.LoginPage import LoginPage
...
loginPage = LoginPage(browser)
...
loginPage.login(loginame, password)
...
```

`.\PageObjects\LoginPage.py`
```python
class LoginPage:

    password_field = (By.ID,'password')
    login_name_field = (By.ID,'user-name')
    login_button = (By.ID,'login-button')

    def __init__(self,driver):
        self.driver = driver
        
    def login(self,name,password):
        self.driver.find_element(*self.login_name_field).send_keys(name)
        self.driver.find_element(*self.password_field).send_keys(password)
        self.driver.find_element(*self.login_button).click()
```

This construction, where everything related to a page or component is separated out, is called the Page Object Model. It's a commonly used automation pattern.

### Advantages:

- **Easy maintainability**: When I need to change something, I change it in one place, not across multiple lines. For example, imagine if the button access changes from `By.ID` to `By.XPATH`, or if the login method changes (e.g., adding captcha).
- **Proximity principle**: Everything that belongs together is grouped together in the code. I don't have to search long or jump around in the code. I understand it quickly.
- **Complex and unreadable code is abstracted** and hidden behind the Page Object Model, enabling the building of a so-called 'Domain Language':

```python
# Examples
loginPage.login('username','password')
menu.logout()
```

- **IntelliSense support**: When writing code, I just need to type:
```python
loginPage.
```
and the editor immediately suggests what I can do (call methods) or what properties (element addresses) I can use.

## Centralization of Addressing, Methods, and Page Parameterization

Simply put, we place repetitive things into variables so they can be set in a clear, centralized manner and controlled without having to rewrite them across multiple lines. When there's really a lot of them, external configuration files can be used.

`test.py`:
```python
...
class TestWebPage:

    test_page = "https://www.saucedemo.com/"
    test_page_inventory = "https://www.saucedemo.com/inventory.html"
    login_error_box = (By.XPATH,"//h3[@data-test='error']")
    ...

    def test_Successful_Login_and_Logout(self, loginame, password):
        ...
        assert self.test_page == browser.current_url
        browser.get(self.test_page_inventory)
        assert self.test_page == browser.current_url
        assert browser.find_element(*self.login_error_box)
        ...
    
    def test_Unsuccessful_Login(self, loginame, password):
        ...
        browser.get(self.test_page)
        ...
        assert browser.find_element(*self.login_error_box)
```

Pay special attention to this construction:
```python
login_error_box = (By.XPATH,"//h3[@data-test='error']")
...
assert browser.find_element(*self.login_error_box)
```

Here we use shared storage of both the method to find an element (`By.ID`/`By.XPATH`...) and its address together in a tuple. Then we use `*` (`*self.login_error_box`) to unpack it as parameters for the given method, so we don't have to write it again at that location. This is a good trick.

## User-Friendly Addressing Method

"Soft addressing" using relative XPATH is also used in one place, exactly according to what the user sees on screen (based on the text 'Logout' in the dropdown menu).
- This tests the given thing as a user would read it (if the text changes => it finds the error)

`Menu.py`:
```python
class Menu:
    ...
    logout_button = (By.XPATH,"//nav/*[text()='Logout']")
    ...

    def logout(self):
        ...
        WebDriverWait(self.driver,2).until(EC.visibility_of_element_located(self.logout_button)).click()
        ...
```

## Explicit Waiting for Dynamic Page Elements

We specifically wait for an element that might not be visible if the page responds slowly. The element is not tied to the loading of the entire page - it's a dynamic thing (slide-out menu on the left side). Therefore, explicit waiting makes sense here. For most elements on this page, we can get by with implicit waiting or a complete loading strategy.

`Menu.py`:
```python
...
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
...

class Menu:
    ...
    def logout(self):
        ...
        WebDriverWait(self.driver,2).until(EC.visibility_of_element_located(self.logout_button)).click()
        ...
```

## Using Pytest Parametrization for Data-Driven Test Approach/Pattern

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

### Advantages this brings:

- **Entry and exit point/state** of the test allows quick execution of all combinations in sequence.
- **The test is also isolated/atomic** - it doesn't care what ran before it or after it.
- **I can run it in any order** relative to other tests.
- **It doesn't matter how I rearrange the rows** in the table.

## Test Design for Login/Logout Verification

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

## Test Logging Using Python Logger and Caplog Usage

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

### Live Logging - How and Why to Simply Display Logs

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

### Why is it good to pay attention to logs from your test and look at them?

- **You'll realize what the test flow looks like** for others, and whether something else needs to be printed/fixed for it to be correct or understandable
- **You'll reveal potential errors** you haven't noticed so far (Maybe it doesn't go through some part of the code at all, or the tested subject - System Under Test - SUT behaves differently than you'd expect)
- **Logs can be used to verify** that the test works as it should
- ...