# Ignored Test

## Description

**Ignored Test** occurs when a test case is intentionally disabled and excluded from execution, typically through mechanisms such as `skip`, `@Skip`, or similar annotations provided by the testing framework. While temporarily ignoring tests may be useful during development or investigation of known issues, permanently ignored tests undermine the reliability of the test suite.

When ignored tests accumulate over time, they create the illusion that the system is adequately tested, while in reality significant portions of expected behavior are no longer being verified.

---

## Symptoms and Impact

The presence of **Ignored Test** may lead to:

- **False Sense of Reliability**: The test suite appears to pass successfully while critical checks are silently bypassed.
- **Hidden Defects**: Bugs covered only by ignored tests may reach production unnoticed.
- **Accumulation of Technical Debt**: Ignored tests often remain disabled indefinitely, increasing maintenance effort and reducing confidence in the test suite.
- **Reduced Test Coverage Transparency**: It becomes unclear which behaviors are actively validated and which are no longer checked.

---

## Identification Criteria

A test is considered an **Ignored Test** if it meets one or more of the following conditions:

- The test is explicitly marked as skipped using `skip`, `@Skip`, or equivalent mechanisms.
- The test contains comments such as *"temporarily disabled"*, *"TODO"*, or *"ignored due to failure"* without a clear resolution plan.
- The test is permanently excluded from execution in continuous integration pipelines.

---

## Code Examples

### Example with Ignored Test

```dart
import 'package:test/test.dart';

class User {
  final int id;
  bool logged = false;

  User(this.id) {
    logged = true;
  }

  bool isLoggedIn() {
    return logged;
  }
}

void main() {
  test('Ignored test example 01', () {
    final user = User(1);
    expect(user.isLoggedIn(), true);
  }, skip: true);

  test('Ignored test example 02', () {
    final user = User(1);
    expect(user.isLoggedIn(), true);
  }, skip: 'Temporarily disabled due to instability');

  test('Active test example', () {
    final user = User(1);
    expect(user.isLoggedIn(), true);
  }, skip: false);
}
```

In this example, two tests are excluded from execution. Unless carefully tracked, these ignored tests may never be re-enabled, silently reducing effective test coverage.

---

### Example without Ignored Test

```dart
import 'package:test/test.dart';

class User {
  final int id;
  bool logged = false;

  User(this.id) {
    logged = true;
  }

  bool isLoggedIn() {
    return logged;
  }
}

void main() {
  test('User should be logged in after creation', () {
    final user = User(1);
    expect(user.isLoggedIn(), true);
  });

  test('User login state should remain true', () {
    final user = User(1);
    expect(user.isLoggedIn(), isTrue);
  });
}
```

In this version, all tests are actively executed, ensuring that expected behavior is continuously verified.

---

## Recommended Refactorings

To mitigate the **Ignored Test** smell, consider the following practices:

* **Avoid Permanent Skips**: Ignoring a test should be a temporary measure, not a long-term solution.
* **Document the Reason Clearly**: When a test must be skipped, provide a precise explanation and reference to an issue, ticket, or task.
* **Define a Resolution Plan**: Establish conditions under which the test will be re-enabled.
* **Monitor Skipped Tests**: Configure CI pipelines to report or fail builds when skipped tests exceed an acceptable threshold.

---

## Exceptions and Special Cases

Ignoring a test may be acceptable when:

* A known defect is under active investigation and the test failure blocks unrelated development.
* External dependencies (e.g., third-party services) are temporarily unavailable.

In such cases, the test should be reactivated as soon as the blocking issue is resolved, and the skip should be treated as a short-lived exception.

---

## Final Remarks

Ignored Tests erode the trustworthiness of test suites over time. Although skipping tests can be useful as a short-term strategy, unmanaged ignored tests compromise test coverage, mask defects, and increase technical debt. A high-quality test suite should minimize skipped tests and ensure that all critical behaviors are continuously validated.

---

## References

* Fowler, M. (1999). *Refactoring: Improving the Design of Existing Code*
* Meszaros, G. (2007). *xUnit Test Patterns: Refactoring Test Code*
* Google Testing Blog (2008). *Flaky Tests: The Good, The Bad, and The Ugly*

