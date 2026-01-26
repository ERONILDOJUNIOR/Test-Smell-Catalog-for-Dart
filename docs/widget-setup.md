# Widget Setup Smell

## Description of the Problem

The **Widget Setup Smell** occurs when widget configurations or initializations are unnecessarily repeated across multiple tests. This increases complexity, reduces code clarity, and makes test maintenance more difficult.

---

## Symptoms and Impact

* **Symptoms**:

  * Duplicate code to set up widgets in multiple tests.
  * Long and hard-to-read tests due to extensive setup.
  * Difficulties updating tests when the widget or its configuration changes.

* **Impact**:

  * Reduces clarity and readability of tests.
  * Increases maintenance effort, especially in large codebases.
  * Hinders the reuse of common configurations.

---

## Identification Criteria

* Repeated blocks of code that are identical or very similar when configuring widgets in tests.
* Configurations that could be extracted into helper methods or utility functions.

---

## Code Examples

### Example with Widget Setup Smell

```dart
import 'package:flutter/material.dart';
import 'package:flutter_test/flutter_test.dart';

void main() {
  testWidgets('Widget title test 1', (WidgetTester tester) async {
    // Repeated widget setup
    await tester.pumpWidget(
      MaterialApp(
        home: Scaffold(
          body: Text('Widget title 1'),
        ),
      ),
    );

    expect(find.text('Widget title 1'), findsOneWidget);
  });

  testWidgets('Widget title test 2', (WidgetTester tester) async {
    // Repeated widget setup
    await tester.pumpWidget(
      MaterialApp(
        home: Scaffold(
          body: Text('Widget title 2'),
        ),
      ),
    );

    expect(find.text('Widget title 2'), findsOneWidget);
  });

  testWidgets('Button test', (WidgetTester tester) async {
    // Repeated widget setup
    await tester.pumpWidget(
      MaterialApp(
        home: Scaffold(
          body: ElevatedButton(
            onPressed: () {},
            child: Text('Press'),
          ),
        ),
      ),
    );

    expect(find.text('Press'), findsOneWidget);
  });
}
```

### Example without Widget Setup Smell

```dart
import 'package:flutter/material.dart';
import 'package:flutter_test/flutter_test.dart';

// Helper method to build test widgets
Widget buildTestWidget({required Widget child}) {
  return MaterialApp(
    home: Scaffold(
      body: child,
    ),
  );
}

void main() {
  testWidgets('Widget title test 1', (WidgetTester tester) async {
    await tester.pumpWidget(buildTestWidget(child: Text('Widget title 1')));
    expect(find.text('Widget title 1'), findsOneWidget);
  });

  testWidgets('Widget title test 2', (WidgetTester tester) async {
    await tester.pumpWidget(buildTestWidget(child: Text('Widget title 2')));
    expect(find.text('Widget title 2'), findsOneWidget);
  });

  testWidgets('Button test', (WidgetTester tester) async {
    await tester.pumpWidget(buildTestWidget(
      child: ElevatedButton(
        onPressed: () {},
        child: Text('Press'),
      ),
    ));
    expect(find.text('Press'), findsOneWidget);
  });
}
```

---

## Suggested Fixes

* **Extract Methods**: Create helper methods to encapsulate repeated widget configurations.
* **Reuse Configurations**: Use design patterns or testing utilities to avoid duplication.
* **Modular Setup**: Implement flexible structures that allow easy reconfiguration of widgets.

---

## Exceptions and Special Cases

* If widgets require highly specific configurations that differ across tests, it may be more appropriate to configure them individually.
* Avoid generalizing setups to the point where the clarity and intent of each test are compromised.

---

## Detection Tools

* **Code Linters**: Tools like `dart_analyze` can help identify duplicated code.
* **Manual Analysis**: Code reviews focused on repeated patterns.

---

## References and Related Studies

* [Flutter Testing Documentation](https://docs.flutter.dev/cookbook/testing)
* Article: *"Optimizing Widget Testing in Flutter"* - [Medium](https://medium.com/)
* Book: *"Refactoring in Flutter Testing"* - technical edition.

---

## Note

The **Widget Setup Smell** is a common problem in Flutter projects but can be easily resolved with good refactoring practices. Doing so improves readability and reduces maintenance effort.
