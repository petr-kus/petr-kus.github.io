(pom-pattern)=
# Page Object Model

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

## Advantages:

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

```{seealso}
For a complete working example, see the {ref}`Level 2 <level-2>` code implementation.
```