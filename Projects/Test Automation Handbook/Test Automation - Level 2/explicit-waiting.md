(explicit-waiting)=
# Explicit Waiting for Dynamic Page Elements

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

## When to Use Explicit Waits

### Dynamic Content Scenarios:
- **Slide-out menus** (like in this example)
- **AJAX-loaded content** that appears after initial page load
- **Modal dialogs** that animate into view
- **Progressive loading** of page sections
- **State-dependent elements** that appear based on user actions

### Common Expected Conditions:
- `visibility_of_element_located()` - Element is present and visible
- `element_to_be_clickable()` - Element is visible and enabled for interaction
- `presence_of_element_located()` - Element exists in DOM (may not be visible)
- `text_to_be_present_in_element()` - Specific text appears in element
- `staleness_of()` - Wait for element to become stale (useful for page transitions)

## Best Practices

### Choose the Right Strategy:
- **Implicit waits**: Set once globally for basic element finding
- **Explicit waits**: Use for specific dynamic behaviors
- **Page load strategies**: Control when driver considers page "loaded"

### Timeout Guidelines:
```python
# Short waits for fast UI interactions
WebDriverWait(self.driver, 2).until(...)

# Medium waits for AJAX requests
WebDriverWait(self.driver, 10).until(...)

# Longer waits for complex operations
WebDriverWait(self.driver, 30).until(...)
```

### Avoid Common Pitfalls:
- Don't mix implicit and explicit waits unnecessarily
- Don't use `time.sleep()` instead of proper waits
- Don't set timeout too short for legitimate loading times
- Don't ignore failed wait exceptions - they indicate real issues

```{tip}
**Performance Impact**: Explicit waits are more efficient than sleep statements because they proceed as soon as the condition is met, rather than waiting for a fixed duration.
```

```{seealso}
Explicit waits work especially well with {ref}`User-Friendly Addressing <user-friendly-addressing>` when dealing with dynamic content that users interact with.
```