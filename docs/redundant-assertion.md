# Redundant Assertion

## Description

**Redundant Assertion** occurs when a test contains assertions whose outcomes are always true or always false, regardless of the system under test. In such cases, the test result does not meaningfully reflect the behavior being validated.

A test should provide a binary outcome that depends on the correctness of the system behavior. Assertions that deterministically fail or pass undermine this purpose and reduce the diagnostic value of the test.

---

## Symptoms and Impact

* **Meaningless Test Results**: The test outcome is predetermined and does not depend on the tested behavior.
* **Reduced Test Clarity**: Legitimate assertions become irrelevant when the test always fails or always passes.
* **Maintenance Overhead**: Developers may waste time debugging failures that are not related to the actual logic under test.

---

## Identification Criteria

To identify **Redundant Assertion**, look for:

* Assertions that are always false or always true (e.g., `expect(true, equals(false))`).
* Tests where the final assertion guarantees failure, regardless of previous assertions or interactions.

---

## Example Code

### Example with Redundant Assertion

```dart
test("should open menu on click", compileComponent(html(), {}, (shadowRoot) {
  print("this never logs :(");
  shadowRoot.click();
  digest();

  var toggleableMenu = shadowRoot.querySelector('.dropdown-menu');
  expect(toggleableMenu.style.display, equals('none'));

  // This assertion always fails, making the entire test meaningless
  expect(true, equals(false));
}));
```

**Problem:**
The final assertion is deterministically false. As a result, the test will always fail, regardless of whether the menu behavior is correct. All previous assertions and interactions become irrelevant.

---

### Example without Redundant Assertion

```dart
test("should open menu on click", compileComponent(html(), {}, (shadowRoot) {
  shadowRoot.click();
  digest();

  var toggleableMenu = shadowRoot.querySelector('.dropdown-menu');
  expect(toggleableMenu.style.display, equals('none'));
}));
```

**Solution:**
Remove assertions that do not depend on the system under test. Each assertion should validate meaningful behavior derived from the test execution.

---

## Recommended Fixes

To mitigate **Redundant Assertion**:

* **Remove Deterministic Assertions**: Eliminate assertions that always evaluate to the same result.
* **Ensure Behavioral Relevance**: Every assertion should depend on the outcome of the test actions.
* **Keep Assertions Intentional**: Assertions should clearly express the expected behavior being validated.

---

## Exceptions and Special Cases

In rare debugging scenarios, temporary assertions may be added to force test failures. However, such assertions must be removed before committing the test, as they invalidate its purpose.

---

## Note

**Redundant Assertion** undermines the credibility of test suites. Removing assertions that always pass or fail ensures that test results accurately reflect the correctness of the system behavior.

---

## References

* Fowler, M. (1999). *Refactoring: Improving the Design of Existing Code*.
* Meszaros, G. (2007). *xUnit Test Patterns: Refactoring Test Code*.
* Van Deursen, A., et al. (2001). "Refactoring Test Code."