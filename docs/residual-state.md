# Residual State Test

## Description

**Residual State Test** occurs when tests leave residual states in the components or services under test, such as widgets, controllers, or state management instances. This problem can lead to intermittent failures, unreliable tests, or unexpected dependencies between test cases.

---

## Symptoms and Impact

* **Symptoms**:

  * Tests fail or behave inconsistently depending on execution order.
  * Error messages indicate that widgets or services remain active.
  * Accumulation of listeners or streams that are not properly closed.

* **Impact**:

  * Increases debugging complexity.
  * Reduces test reliability and independence.
  * May cause memory leaks due to unreleased resources.

---

## Identification Criteria

* Widgets or services with unclosed lifecycle objects (e.g., `Stream`, `Future`, or `Controller`).
* Missing calls to `dispose()` in widget tests.
* Debugging shows accumulation of listeners or uncollected objects.

---

## Example Code

### Example with Residual State Test

File: `residual_state_test_with_smell.dart`

```dart
import 'package:flutter/material.dart';
import 'package:flutter_test/flutter_test.dart';

void main() {
  testWidgets('Test with residual state', (WidgetTester tester) async {
    // Initial widget setup with a controller
    final controller = TextEditingController();
    await tester.pumpWidget(MaterialApp(
      home: Scaffold(
        body: TextField(controller: controller),
      ),
    ));

    // Simulate text input
    await tester.enterText(find.byType(TextField), 'Flutter');
    expect(controller.text, 'Flutter');

    // Test ends without disposing the controller
  });

  testWidgets('Another test that may fail due to residual state', (WidgetTester tester) async {
    // Reusing a controller from previous test may lead to failure
    final controller = TextEditingController();
    expect(controller.text.isEmpty, true); // May fail due to residual state
  });
}
```

---

### Example without Residual State Test

File: `residual_state_test_without_smell.dart`

```dart
import 'package:flutter/material.dart';
import 'package:flutter_test/flutter_test.dart';

void main() {
  testWidgets('Test without residual state', (WidgetTester tester) async {
    // Initial widget setup with a controller
    final controller = TextEditingController();
    await tester.pumpWidget(MaterialApp(
      home: Scaffold(
        body: TextField(controller: controller),
      ),
    ));

    // Simulate text input
    await tester.enterText(find.byType(TextField), 'Flutter');
    expect(controller.text, 'Flutter');

    // Dispose the controller at the end of the test
    controller.dispose();
  });

  testWidgets('Another test that is independent', (WidgetTester tester) async {
    // New controller created without dependency on previous state
    final controller = TextEditingController();
    expect(controller.text.isEmpty, true);
    controller.dispose();
  });
}
```

---

## Recommended Fixes

* Always use methods like `dispose()` to close widgets or services, such as `TextEditingController` or `StreamController`.
* Properly configure the test environment before and after each test using `setUp` and `tearDown`.
* Avoid reusing shared instances between tests; create new instances for each test case.

---

## Exceptions and Special Cases

* In some cases, testing frameworks automatically manage widget and resource states. However, it is good practice to explicitly close critical instances.
* When testing external dependencies (e.g., mock databases), ensure they are reset between tests.

---

## References

* [Flutter Official Documentation on Lifecycle](https://docs.flutter.dev/)
* Article: *"Effective Widget Testing in Flutter"* – [Medium](https://medium.com/)
* Book: *"Test Smells in Dart and Flutter"* (academic edition)

---

## Note

This problem is particularly relevant for Flutter applications that rely on high performance and efficient resource management. A disciplined approach ensures more reliable and independent tests.