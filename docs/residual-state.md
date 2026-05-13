# Residual State

## Problem Description

**Residual State** is a test smell that occurs when a test modifies shared global or singleton state, such as static class fields, service locator instances (e.g., `GetIt`), or framework-level registries, without restoring that state after execution. This causes subsequent tests to operate under unexpected preconditions, leading to order-dependent failures, intermittent results, and tests that are difficult to reason about in isolation.

In the Flutter ecosystem, Residual State is particularly prevalent because widget-level state (e.g., `ChangeNotifier`, `StreamController`, `TextEditingController`) is often instantiated at class-level scope or via `setUpAll`, and developers frequently omit the corresponding `dispose()` or cleanup call in `tearDown`.

This smell was identified during the analysis of real-world Flutter projects during the validation phase of this research, where it was observed that certain test files produced inconsistent results depending on the execution order, a classic symptom of state pollution across test boundaries.

---

## Symptoms and Impact

* **Symptoms**:

  * Tests fail or behave differently depending on execution order.
  * Warnings such as `"A listener was added to a Stream after it was closed"` or `"FlutterError: A TextEditingController was used after being disposed"` appear during test runs.
  * Shared static variables or singletons retain values set by previous tests.
  * `tearDown` or `tearDownAll` blocks are absent when mutable shared resources are present.

* **Impact**:

  * Severely reduces **test independence**, a fundamental property of a reliable test suite.
  * Leads to **flaky tests**: tests that pass individually but fail when run as part of the full suite.
  * Increases **debugging complexity**, as the root cause of a failure lies in a different test than the one that fails.
  * May cause **memory leaks** or resource exhaustion in long test runs due to unreleased `StreamController`, `AnimationController`, or platform channels.

---

## Identification Criteria

A test can be considered to exhibit **Residual State** if any of the following conditions are met:

1. A **mutable static field** or **singleton** (e.g., via `GetIt`, `sl`, `locator`) is modified within a `test` or `testWidgets` block without a corresponding reset in `tearDown` or `tearDownAll`.
2. A **disposable object** (e.g., `TextEditingController`, `StreamController`, `AnimationController`, `ChangeNotifier`) is declared at the `group` or file scope but not disposed after each test.
3. A **platform channel** or **shared service** is configured in one test and not restored before the next.
4. The test suite produces different results when tests are executed in a different order.

---

## Code Examples

### Example with Residual State

```dart
import 'package:flutter/material.dart';
import 'package:flutter_test/flutter_test.dart';

// Controller declared at group scope — never disposed between tests
final controller = TextEditingController();

void main() {
  testWidgets('First test — sets controller text', (WidgetTester tester) async {
    await tester.pumpWidget(MaterialApp(
      home: Scaffold(body: TextField(controller: controller)),
    ));
    await tester.enterText(find.byType(TextField), 'residual value');
    expect(controller.text, 'residual value');
    // controller.dispose() is NEVER called — residual state leaks into next test
  });

  testWidgets('Second test — assumes fresh controller', (WidgetTester tester) async {
    // Fails or produces unexpected behavior because controller.text == 'residual value'
    expect(controller.text.isEmpty, isTrue); // ← This assertion may fail
  });
}
```

### Example without Residual State

```dart
import 'package:flutter/material.dart';
import 'package:flutter_test/flutter_test.dart';

void main() {
  late TextEditingController controller;

  setUp(() {
    controller = TextEditingController(); // Fresh instance per test
  });

  tearDown(() {
    controller.dispose(); // Cleanup after each test
  });

  testWidgets('First test — sets controller text', (WidgetTester tester) async {
    await tester.pumpWidget(MaterialApp(
      home: Scaffold(body: TextField(controller: controller)),
    ));
    await tester.enterText(find.byType(TextField), 'hello');
    expect(controller.text, 'hello');
  });

  testWidgets('Second test — independent fresh state', (WidgetTester tester) async {
    expect(controller.text.isEmpty, isTrue); // ✓ Always passes
  });
}
```

---

## Recommended Fixes

1. **Use `setUp`/`tearDown`**: Always create disposable objects inside `setUp` and destroy them in `tearDown`, ensuring each test starts with a clean state.
2. **Avoid shared mutable state**: Declare all mutable variables as `late` within the test scope, never as class-level or file-level fields.
3. **Reset service locators**: If using `GetIt` or similar, call `getIt.reset()` in `tearDown` or use `getIt.unregister<T>()` per test.
4. **Close streams and controllers**: Always call `.close()` on `StreamController` and `.dispose()` on `ChangeNotifier` instances after each test.
5. **Use `addTearDown`**: For resources created mid-test, use `addTearDown(() => resource.dispose())` immediately after creation.

```dart
test('example with addTearDown', () {
  final controller = StreamController<int>();
  addTearDown(controller.close); // Guaranteed cleanup even if test throws
  // ...
});
```

---

## Exceptions and Special Cases

* **Read-only singletons**: If a singleton is used read-only within tests (e.g., a constant configuration object) and is never mutated, it does not constitute Residual State. Only write access to shared state qualifies.
* **Framework-managed state**: Some Flutter test utilities (e.g., `tester.pumpWidget`) internally manage widget lifecycle. These are excluded from this smell's scope unless the developer explicitly holds external references to internal state.
* **Intentional shared fixtures**: If a `setUpAll` / `tearDownAll` pair explicitly manages the full lifecycle of a shared resource (e.g., an in-memory database), this is an acceptable pattern and not a smell.

---

## Detection Tools

* **`dart analyze`**: Can detect some unreferenced disposable objects when combined with custom lint rules.
* **Manual code review**: Inspection of `setUp`/`tearDown` pairing and shared variable usage across test blocks.

---

## Empirical Evidence

This smell was identified as a distinct Dart/Flutter-specific pattern during the **Validation Phase** of this research, through the manual analysis of real-world Flutter repositories. In the controlled validation dataset, 10 intentional positive instances were constructed based on patterns observed in open-source Flutter projects, covering: `TextEditingController` leaks, unreset `GetIt` registrations, and unclosed `StreamController` instances across test groups. The DNose 2.1.0 detector achieved an **F1-Score of 0.900** on this dataset.

---

## References and Related Studies

* Flutter Official Documentation: [Testing — Widgets](https://docs.flutter.dev/cookbook/testing/widget/introduction)
* Wohlin, C. et al. (2012). *Experimentation in Software Engineering*. Springer.
* Peruma, A. et al. (2020). *An Exploratory Characterization of Bad Testing Practices Using the Test Smell Detector (TsDetect)*. EASE.
* Virgínio, T. et al. (2021). *JNose Test: A Tool for Detecting Test Smells in Java*. CBSOFT.

---

## Note

**Residual State** is one of three test smells identified specifically for the Dart/Flutter ecosystem in this research, alongside *Expected Resolution Omission (ERO)* and *Widget Setup Smell*. Its high prevalence in Flutter projects is linked to the stateful, lifecycle-driven nature of the framework, where developers must manually manage resource disposal, a responsibility that is easily overlooked under test conditions.