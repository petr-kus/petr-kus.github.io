(centralized-config)=
# Centralization of Addressing, Methods, and Page Parameterization

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

## Key Pattern: Tuple Unpacking for Element Locators

Pay special attention to this construction:
```python
login_error_box = (By.XPATH,"//h3[@data-test='error']")
...
assert browser.find_element(*self.login_error_box)
```

Here we use shared storage of both the method to find an element (`By.ID`/`By.XPATH`...) and its address together in a tuple. Then we use `*` (`*self.login_error_box`) to unpack it as parameters for the given method, so we don't have to write it again at that location. This is a good trick.

## Benefits

- **Single source of truth**: URLs, element locators, and other constants are defined once
- **Easy maintenance**: Change a URL or locator in one place, it updates everywhere
- **Cleaner code**: Tests become more readable without hardcoded values scattered throughout
- **Configuration flexibility**: Easy to extend with external config files for different environments

```{seealso}
This pattern works hand-in-hand with {ref}`POM Pattern <pom-pattern>` for comprehensive test organization.
```