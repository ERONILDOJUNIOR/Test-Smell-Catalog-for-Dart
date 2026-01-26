# Assertion Roulette

## Description

**Assertion Roulette** occurs when a test method contains multiple assertions without descriptive messages or sufficient contextual information. In such cases, when a test fails, it becomes unclear which assertion caused the failure and for what reason. This significantly reduces the readability, diagnosability, and maintainability of test code.

The term reflects the uncertainty faced by developers when interpreting failing tests, as they are effectively forced to "guess" which condition was violated.

---

## Symptoms and Impact

The presence of **Assertion Roulette** may lead to the following issues:

* **Reduced Debugging Efficiency**: When an assertion fails, the lack of contextual information makes it difficult to quickly identify the root cause, particularly in tests with multiple assertions.
* **Increased Maintenance Effort**: Tests affected by this smell are harder to understand and modify, increasing long-term maintenance costs.
* **Poor Readability**: The intent of the test becomes unclear, reducing its value as documentation of expected system behavior.

---

## Identification Criteria

A test is likely to exhibit **Assertion Roulette** if it meets one or more of the following conditions:

* The test method contains multiple assertions without descriptive failure messages.
* The assertions do not clearly communicate which specific condition or requirement is being verified.

---

## Code Examples

### Example with Assertion Roulette

```dart
import 'package:flutter_test/flutter_test.dart'; // Or 'package:test/test.dart' for pure unit tests

void main() {
  test('Test with Assertion Roulette', () {
    final values = [10, 20, 30];

    expect(values.length, 4);
    expect(values[0], 5);
    expect(values.contains(50), true);
  });
}
```

In this example, if the test fails, it is not immediately clear which expectation failed or why.

### Example without Assertion Roulette

```dart
import 'package:flutter_test/flutter_test.dart'; // Or 'package:test/test.dart' for pure unit tests

void main() {
  test('Test without Assertion Roulette', () {
    final values = [10, 20, 30];

    expect(values.length, 4, reason: 'The list is expected to contain exactly four elements');
    expect(values[0], 5, reason: 'The first element of the list is expected to be 5');
    expect(values.contains(50), true, reason: 'The list is expected to contain the value 50');
  });
}
```

By providing explicit reasons, each assertion clearly communicates its intent, making failures easier to diagnose.

---

## Recommended Refactorings

To mitigate **Assertion Roulette**, the following practices are recommended:

* **Provide Descriptive Assertion Messages**: Use the `reason` parameter (or equivalent) to explain the expected condition and its purpose.
* **Limit the Number of Assertions per Test**: When feasible, split complex tests into smaller, focused tests that validate a single behavior.
* **Use Explicit Verification Strategies**: In more complex scenarios, consider employing mocking frameworks (e.g., `mockito`) to isolate behaviors and make assertions more precise and meaningful.

---

## Exceptions and Special Cases

In simple or trivial tests containing a single assertion, the absence of a descriptive message may be acceptable. However, for any test that validates multiple conditions, providing explicit context is strongly recommended.

---

## Notes

**Assertion Roulette** is particularly prevalent in large or complex test suites, where tests often validate multiple aspects of system behavior. Addressing this smell improves test clarity, facilitates faster debugging, and enhances overall test quality.

---

## Practical Considerations and Detection Guidelines

### Role of `verify` in Dart Tests

In the Dart testing ecosystem, particularly when using mocking frameworks such as `mockito`, the `verify` function is commonly used to assert interactions with mocked dependencies (for example, whether a method was called, how many times it was invoked, or with which arguments).

For the purpose of **Assertion Roulette** detection, calls to `verify` are **not considered assertions**. Only `expect` statements are taken into account when identifying this smell.

This distinction is deliberate. While `expect` validates observable outcomes and system state, `verify` focuses on interaction-based behavioral checks. Including `verify` calls in the assertion count would lead to false positives and reduce the precision of the detection strategy.

---

### Number of `expect` Statements and Smell Classification

The detection of **Assertion Roulette** depends on both the number of `expect` statements and the presence (or absence) of explicit diagnostic context, typically provided through the `reason` parameter.

The following rules apply:

* A test may contain **exactly one `expect` statement without a `reason`** and still **not** be classified as Assertion Roulette.

  * In this case, it is assumed that the **test name itself** provides sufficient semantic context to describe the intent of the assertion.

* If a test contains **two or more `expect` statements without a `reason`**, the Assertion Roulette smell is present.

  * Each additional undocumented assertion increases ambiguity and makes failure diagnosis more difficult.

* If a test contains multiple `expect` statements and **only one lacks a `reason`**, the test is **not** considered to exhibit Assertion Roulette.

  * The undocumented assertion is treated as the primary validation described by the test name.

* `verify` statements are **ignored** when counting assertions for smell detection.

---

### Summary Rule

A Dart test exhibits the **Assertion Roulette** smell if and only if it contains:

* **More than one `expect` statement without an explicit `reason` parameter**, regardless of the number of `verify` calls present.

This rule balances practical testing conventions in Dart with the need for clear and diagnosable test failures.

---

## References

* Fowler, M. (1999). *Refactoring: Improving the Design of Existing Code*.
* Meszaros, G. (2007). *xUnit Test Patterns: Refactoring Test Code*.
* Van Deursen, A., et al. (2001). "Refactoring Test Code."'