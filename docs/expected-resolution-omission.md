# Expected Resolution Omission (ERO)

## Problem Description

**Expected Resolution Omission (ERO)** is a test smell that occurs when there are mistakes in the proper use of `await`, `expect`, or `expectLater` in asynchronous tests in Dart. This issue can lead to:

* **Tests passing incorrectly**, even when behaviors are wrong.
* **Failures due to synchronization issues**, where the tested code is not executed at the correct time.
* **Unnecessary use of `await`** in contexts that do not require it, making the test confusing or redundant.

Tests that do not properly wait for a `Future` to resolve or that misuse `await` may mask errors, resulting in ineffective validation of asynchronous behavior.

---

## Symptoms and Impact

* **False positives**: Tests that pass even when the expected behavior is incorrect.
* **Flaky tests**: Inconsistent results due to improper synchronization with asynchronous code.
* **Logic errors**: Misuse of `await` can cause misinterpretation of test flow.
* **Confusing code**: Inappropriate awaits or matchers make the expected behavior harder to understand.

---

## Identification Criteria

1. **Expectation without `await` or `expectLater`**:

   * When a `Future` is expected to return a value, but there is no `await` or `expectLater` to validate its resolution.

2. **Using `await` on non-asynchronous code**:

   * Awaiting values or operations that are not `Future`, introducing redundancy.

3. **Missing `throwsA` when testing exceptions**:

   * Testing a `Future` that throws an exception without using the `throwsA` matcher.

4. **Incorrect use of `completes`**:

   * Using `expect` with `await` instead of `expectLater` with `completes` to check that a `Future` completes successfully.

5. **Ignoring asynchronous behavior**:

   * Testing a `Future` directly without `await` or `expectLater`, leading to incorrect results or false positives.

---

## Code Examples

### Problematic Example

```dart
void testExample() {
  // Example 1: Missing await or expectLater
  final futureResult = Future.value(42);
  expect(futureResult, equals(42)); // Missing await or expectLater

  // Example 2: Unnecessary await
  final result = 42;
  expect(await Future.value(result), equals(42)); // Unnecessary await

  // Example 3: Missing throwsA
  final futureError = Future.error(Exception('Failed'));
  expect(await futureError, isA<Exception>()); // Incorrect matcher

  // Example 4: Incorrect use of completes
  final futureResult2 = Future.value(42);
  expect(await futureResult2, completes); // Incorrect matcher

  // Example 5: Ignoring asynchronous behavior
  final delayedResult = Future.delayed(Duration(seconds: 1), () => 42);
  expect(delayedResult, equals(42)); // Without await, may pass incorrectly
}
```

### Corrected Example

```dart
void testExample() async {
  // Correction 1: Expectation with await or expectLater
  final futureResult = Future.value(42);
  expect(await futureResult, equals(42)); // Using await
  // or
  expectLater(futureResult, completes);  // Using expectLater

  // Correction 2: Avoid await on non-async code
  final result = 42;
  expect(result, equals(42)); // No await needed

  // Correction 3: Use throwsA for exception testing
  final futureError = Future.error(Exception('Failed'));
  expect(futureError, throwsA(isA<Exception>())); // Correct matcher

  // Correction 4: Validate Future completion
  final delayedResult = Future.delayed(Duration(seconds: 1), () => 42);
  expectLater(delayedResult, completes); // Correct matcher

  // Correction 5: Synchronize with await or expectLater
  final delayedResult2 = Future.delayed(Duration(seconds: 1), () => 42);
  expect(await delayedResult2, equals(42)); // Ensures proper synchronization
}
```

---

## Recommended Fixes

1. Always use `await` or `expectLater` to wait for a `Future` to resolve.
2. Avoid using `await` on non-asynchronous values; validate them directly.
3. For testing exceptions, use the `throwsA` matcher with the expected type.
4. Use `expectLater` with the `completes` matcher to ensure a `Future` completes without errors.
5. Ensure all asynchronous behavior is awaited before validating results.

---

## Exceptions and Special Cases

* If asynchronous behavior is not critical to the test, a synchronous mock may be acceptable.
* In scenarios where resolving a `Future` is unnecessary, it is acceptable to skip `await` or `expectLater` if clearly documented.

---

## Detection Tools

* **Linters**: Custom rules can detect misuse of `await` or incorrect matchers in async tests.
* **Static analyzers**: Tools like `dart analyze` can be configured to identify common mistakes in asynchronous test code.

---

## References

* Dart Official Documentation: [Asynchronous Testing](https://dart.dev/guides/testing/async)
* Best practices articles on Dart and Flutter testing.
* Academic studies on test smells and asynchronous code solutions.

---

## Note

Identifying and fixing **Expected Resolution Omission** is crucial for reliable and robust tests in Dart/Flutter, particularly for asynchronous code.
