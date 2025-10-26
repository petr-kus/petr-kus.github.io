(user-friendly-addressing)=
# User-Friendly Addressing Method

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

## Why This Approach Matters

### User-Centric Testing
When you select elements based on the text users actually see, your tests become more realistic user simulations. If the visible text changes, your test catches it immediately.

### Advantages:
- **Realistic user simulation**: Tests interact with elements the same way users identify them
- **Content validation**: If button text changes unexpectedly, tests will fail appropriately  
- **Language/localization awareness**: Easy to spot when translations or content updates break functionality
- **Self-documenting**: The test code clearly shows what the user sees

### Considerations:
- **Stability**: Text-based selectors can be more fragile than ID-based ones
- **Localization impact**: Different languages may require different test approaches
- **Performance**: XPath text searches can be slower than ID/class searches

## Best Practices

Use user-friendly addressing when:
- Testing user-facing functionality where text content is critical
- Validating that the right content appears to users
- Building domain-specific language in your tests

Combine with stable selectors (IDs, classes) for:
- Technical functionality that doesn't depend on user-visible text
- Elements that frequently change text content
- Performance-critical test scenarios

```{seealso}
This technique pairs well with {ref}`Explicit Waiting <explicit-waiting>` for dynamic content and contributes to the domain language goals of {ref}`POM Pattern <pom-pattern>`.
```