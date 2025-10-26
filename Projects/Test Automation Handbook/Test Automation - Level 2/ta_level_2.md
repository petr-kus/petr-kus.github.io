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

## Key Concepts Covered

This Level 2 example demonstrates several important automation patterns and techniques:

- {ref}`POM Pattern <pom-pattern>` - Page Object Model implementation
- {ref}`Centralized Configuration <centralized-config>` - Addressing and parameterization
- {ref}`User-Friendly Addressing <user-friendly-addressing>` - Readable element selection
- {ref}`Explicit Waiting <explicit-waiting>` - Dynamic element handling
- {ref}`Data-Driven Testing <data-driven-testing>` - Pytest parametrization
- {ref}`Test Design Patterns <test-design-patterns>` - Login/logout verification
- {ref}`Test Logging <test-logging>` - Python logger and caplog usage

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

~~~python
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
~~~
```

```{tab-item} Browser.py
:sync: browser-py

~~~python
class Browser:
    def __init__(self,driver):
        self.driver = driver

    def go_to_page(self,page):
        self.driver.get(page)
    
    def page(self):
        return self.driver.current_url
~~~
```

```{tab-item} LoginPage.py
:sync: loginpage-py

~~~python
from selenium import webdriver
from selenium.webdriver.common.by import By

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
~~~
```

```{tab-item} Menu.py
:sync: menu-py

~~~python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

class Menu:
    
    hamburger_menu = (By.ID,"react-burger-menu-btn")
    logout_button = (By.XPATH,"//nav/*[text()='Logout']")

    def __init__(self,driver):
        self.driver = driver

    def logout(self):
        self.driver.find_element(*self.hamburger_menu).click()
        WebDriverWait(self.driver,2).until(EC.visibility_of_element_located(self.logout_button)).click()
~~~
```
````

```{seealso}
For detailed explanations of the patterns and techniques used in this code, explore the individual concept guides linked above.
```