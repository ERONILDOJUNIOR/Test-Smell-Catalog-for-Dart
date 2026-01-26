# Sleepy Test

## Description

**Sleepy Test** occurs when a test relies on artificial delays, most commonly implemented using fixed-time waits such as `sleep` or `Future.delayed`, to allow asynchronous operations to complete. Instead of synchronizing on observable conditions or events, the test pauses execution for a predetermined amount of time and then performs assertions.

This practice is fragile and inefficient. It assumes that the system under test will always complete its work within the specified delay, an assumption that often breaks under different execution environments, loads, or hardware configurations.

---

## Symptoms and Impact

The presence of **Sleepy Test** typically leads to the following problems:

* **Performance Degradation**: Fixed delays unnecessarily slow down test execution, increasing the overall runtime of the test suite.
* **Flaky Tests**: Tests may pass or fail nondeterministically depending on timing variations, system load, or execution speed.
* **Poor Maintainability**: When system performance changes, delay values must be manually adjusted, making tests brittle and harder to evolve.

---

## Identification Criteria

A test is likely to exhibit the **Sleepy Test** smell if it includes:

* Explicit calls to `sleep`, `Future.delayed`, or similar time-based waiting mechanisms.
* Asynchronous tests that wait for a fixed duration instead of synchronizing on a specific condition, event, or completion signal.

---

## Code Examples

### Example with Sleepy Test

```dart
import 'package:test/test.dart';

void main() {
  test('Sleepy Test with fixed delay', () async {
    final dataFetcher = DataFetcher();
    dataFetcher.fetchData();

    // Fixed wait time
    await Future.delayed(const Duration(seconds: 5));

    expect(dataFetcher.isDataLoaded, isTrue,
        reason: 'Data should be loaded after the delay');
  });
}

class DataFetcher {
  bool isDataLoaded = false;

  Future<void> fetchData() async {
    await Future.delayed(const Duration(seconds: 5));
    isDataLoaded = true;
  }
}
```

In this example, the test assumes that five seconds is sufficient for the asynchronous operation to complete. If execution is slower or faster than expected, the test becomes unreliable or unnecessarily slow.

---

### Example without Sleepy Test

```dart
import 'package:test/test.dart';

void main() {
  test('Synchronized async test', () async {
    final dataFetcher = DataFetcher();

    await dataFetcher.fetchData();

    expect(dataFetcher.isDataLoaded, isTrue,
        reason: 'Data should be loaded once fetchData completes');
  });
}

class DataFetcher {
  bool isDataLoaded = false;

  Future<void> fetchData() async {
    await Future.delayed(const Duration(seconds: 3));
    isDataLoaded = true;
  }
}
```

Here, the test synchronizes directly on the completion of the asynchronous operation, eliminating the need for arbitrary waiting periods.

---

## Recommended Refactorings

To eliminate the **Sleepy Test** smell, consider the following strategies:

* **Synchronize on Completion**: Await the completion of `Future`s or asynchronous methods instead of waiting for a fixed time.
* **Wait for Conditions or Events**: Prefer condition-based synchronization (e.g., polling with a timeout) over fixed delays when direct awaiting is not possible.
* **Use Testing Utilities**: Leverage testing frameworks and utilities designed for asynchronous coordination, such as stream listeners, callbacks, or scheduler controls.

---

## Exceptions and Special Cases

In rare cases, such as testing interactions with hard real-time systems or external components with strictly deterministic timing, a fixed delay may be unavoidable. Even in such scenarios, delays should be minimized and clearly documented to justify their necessity.

---

## Notes on Asynchronous Testing in Dart

**Sleepy Test** most commonly appears in tests involving asynchronous behavior. Dart provides several mechanisms to write deterministic asynchronous tests without relying on fixed delays.

In particular:

* Prefer `await` on asynchronous APIs whenever possible.
* Use `expectLater` to assert future or stream-based outcomes in a structured and time-aware manner.

For example, `expectLater` allows assertions to be bound directly to asynchronous results, reducing the temptation to introduce artificial delays and improving both reliability and clarity.

---

## References

* Fowler, M. (1999). *Refactoring: Improving the Design of Existing Code*.
* Meszaros, G. (2007). *xUnit Test Patterns: Refactoring Test Code*.
* Van Deursen, A., et al. (2001). *Refactoring Test Code*.
