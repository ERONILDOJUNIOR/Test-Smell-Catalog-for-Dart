# Duplicate Assert

## Description

**Duplicate Assert** occurs when a Dart test contains multiple assertions (`expect`) that validate the same condition or behavior. This usually indicates redundant test code, where a single condition is verified repeatedly, increasing test complexity without providing additional value.

**Duplicate Assert** compromises test clarity and can hinder maintenance, since a change in one assertion may not be reflected in its duplicate, leading to inconsistencies and a false sense of test coverage.

---

## Symptoms and Impact

* **Code Redundancy**: Tests become longer and harder to understand while validating the same condition multiple times.
* **Maintenance Difficulty**: Changes to the expected behavior may be updated in only one assertion, causing inconsistent test logic.
* **Test Noise**: Duplicate assertions add unnecessary noise, making it harder to identify what behavior is actually under test.

---

## Identification Criteria

To identify **Duplicate Assert**, look for:

* Tests containing multiple assertions (`expect`) that verify the same variable, property, or condition in an identical and redundant way.
* Conditions or behaviors tested more than once within the same test method without a clear justification, such as an intermediate state change.

---

## Example Code

### Example with Duplicate Assert

```dart
import 'package:flutter_test/flutter_test.dart';

class User {
  final String name;

  User({required this.name});
}

void main() {
  test('User name test with duplicate assertions', () {
    var user = User(name: "John");

    expect(user.name, equals("John")); // First assertion
    expect(user.name, equals("John")); // Redundant assertion
    // A failure in the first assertion already invalidates the test
  });
}
```

**Problem:**
Both assertions validate exactly the same condition. The second assertion provides no additional verification and increases redundancy.

---

### Example without Duplicate Assert

```dart
import 'package:flutter_test/flutter_test.dart';

class User {
  final String name;

  User({required this.name});
}

void main() {
  test('User name test', () {
    var user = User(name: "John");

    expect(
      user.name,
      equals("John"),
      reason: "The user's name should be 'John'",
    );
  });
}
```

**Solution:**
A single, clear assertion is sufficient to validate the expected behavior.

---

## Recommended Fixes

To mitigate **Duplicate Assert**:

* **Remove Redundant Assertions**: Ensure each assertion validates a unique condition or behavior.
* **Centralize Verification**: Perform each logical check only once, in a clear and explicit manner.
* **Refactor Test Structure**: If repetition is tempting due to complex logic or state changes, consider splitting the test into smaller, focused tests or refactoring the setup logic.

---

## Exceptions and Special Cases

In specific scenarios, validating similar conditions at different points in a test may be justified if there is an intermediate state change. Examples include:

* Verifying state before and after an operation to ensure correct state transition.
* Integration tests where the same data is validated across different stages of a workflow.

Pure duplication—where the same assertion is executed at the same logical point without any state change—should be avoided.

---

## Note

**Duplicate Assert** is commonly found in hastily written or poorly structured tests. Removing redundant assertions improves test readability, maintainability, and confidence in test results.

---

## References

* Fowler, M. (1999). *Refactoring: Improving the Design of Existing Code*.
* Meszaros, G. (2007). *xUnit Test Patterns: Refactoring Test Code*.
* Van Deursen, A., et al. (2001). "Refactoring Test Code."
