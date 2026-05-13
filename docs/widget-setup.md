# Widget Setup Smell

## Problem Description

**Widget Setup Smell** occurs when the code responsible for building and pumping a widget under test is duplicated across multiple `testWidgets` blocks within the same test file — or, more severely, across multiple test files — without being extracted into a reusable helper function, factory, or testing utility. This leads to fragile, verbose, and hard-to-maintain widget tests.

Unlike the *General Fixture* smell (which concerns initialization of shared data structures), Widget Setup Smell is specific to the Flutter testing infrastructure: it involves the repeated use of `tester.pumpWidget(...)` calls with identical or near-identical widget trees, often including `MaterialApp`, `Scaffold`, `ThemeData`, routing configuration, and provider/locator wrappers that must be reproduced verbatim across tests.

This smell was identified during the **Validation Phase** of this research through the analysis of real-world Flutter repositories, where it was observed that a significant number of widget test files contained 3 or more `testWidgets` blocks each replicating the same `MaterialApp` scaffolding tree — resulting in test code that was several times longer than necessary and that broke across multiple tests whenever the widget signature changed.

---

## Symptoms and Impact

* **Symptoms**:

  * Three or more `testWidgets` blocks within the same group contain identical or near-identical `tester.pumpWidget(...)` calls.
  * The widget tree passed to `pumpWidget` includes framework boilerplate (e.g., `MaterialApp`, `Scaffold`, provider wrappers) that is not specific to any single test.
  * Any change to the widget constructor or theming requires updating every affected `testWidgets` block individually.
  * Test files become disproportionately long relative to the actual behavior being verified.

* **Impact**:

  * **Maintainability**: A widget API change propagates as a breaking change across many test blocks, rather than requiring a single update to a shared factory.
  * **Readability**: The intent of each individual test is obscured by repetitive setup boilerplate, making it harder to identify what behavior is actually being verified.
  * **Reliability**: Inconsistencies arise when one occurrence of the duplicated setup is updated but others are not, producing a test suite that verifies different configurations for the same widget.
  * **Reusability**: Test utilities cannot be shared across files without duplicating the boilerplate further.

---

## Identification Criteria

A test file exhibits **Widget Setup Smell** if any of the following conditions are present:

1. The same `tester.pumpWidget(...)` expression (with the same widget type and primary parameters) appears in **three or more** `testWidgets` blocks in the same `group` or `main` function.
2. Widget tree construction code exceeds 5 lines and is repeated verbatim across test blocks.
3. Framework wrapper components (`MaterialApp`, `ChangeNotifierProvider`, `BlocProvider`, `GetMaterialApp`, etc.) are repeated as boilerplate without parameterization.
4. There is no helper function, `setUp` block, or factory method responsible for widget construction within the test file.

---

## Code Examples

### Example with Widget Setup Smell

```dart
import 'package:flutter/material.dart';
import 'package:flutter_test/flutter_test.dart';
import 'package:my_app/widgets/product_card.dart';

void main() {
  testWidgets('shows product name', (WidgetTester tester) async {
    // ↓ Repeated boilerplate — 6 lines for widget setup
    await tester.pumpWidget(
      MaterialApp(
        home: Scaffold(
          body: ProductCard(name: 'Laptop', price: 1299.99),
        ),
      ),
    );
    expect(find.text('Laptop'), findsOneWidget);
  });

  testWidgets('shows product price', (WidgetTester tester) async {
    // ↓ Identical boilerplate repeated
    await tester.pumpWidget(
      MaterialApp(
        home: Scaffold(
          body: ProductCard(name: 'Laptop', price: 1299.99),
        ),
      ),
    );
    expect(find.text('R\$ 1.299,99'), findsOneWidget);
  });

  testWidgets('shows add to cart button', (WidgetTester tester) async {
    // ↓ Yet again the same boilerplate
    await tester.pumpWidget(
      MaterialApp(
        home: Scaffold(
          body: ProductCard(name: 'Laptop', price: 1299.99),
        ),
      ),
    );
    expect(find.byIcon(Icons.add_shopping_cart), findsOneWidget);
  });
}
```

### Example without Widget Setup Smell

```dart
import 'package:flutter/material.dart';
import 'package:flutter_test/flutter_test.dart';
import 'package:my_app/widgets/product_card.dart';

/// Helper factory: widget setup extracted once, reused by all tests.
Widget buildProductCard({
  String name = 'Laptop',
  double price = 1299.99,
}) {
  return MaterialApp(
    home: Scaffold(
      body: ProductCard(name: name, price: price),
    ),
  );
}

void main() {
  testWidgets('shows product name', (WidgetTester tester) async {
    await tester.pumpWidget(buildProductCard());
    expect(find.text('Laptop'), findsOneWidget);
  });

  testWidgets('shows product price', (WidgetTester tester) async {
    await tester.pumpWidget(buildProductCard());
    expect(find.text('R\$ 1.299,99'), findsOneWidget);
  });

  testWidgets('shows add to cart button', (WidgetTester tester) async {
    await tester.pumpWidget(buildProductCard());
    expect(find.byIcon(Icons.add_shopping_cart), findsOneWidget);
  });

  testWidgets('shows custom product name', (WidgetTester tester) async {
    await tester.pumpWidget(buildProductCard(name: 'Headphones', price: 299.00));
    expect(find.text('Headphones'), findsOneWidget);
  });
}
```

---

## Recommended Fixes

1. **Extract a widget builder function**: Create a named helper function (e.g., `buildWidgetUnderTest(...)`) that encapsulates the full `tester.pumpWidget(...)` call. This function should accept parameters for the aspects that legitimately vary between tests.
2. **Use `setUp` for shared pumping**: When all tests in a `group` use the same widget configuration, pump the widget once in a `setUp` block paired with the test group.
3. **Create a shared test utility file**: For large applications, define widget factories in a separate `test/helpers/` directory and import them across test files.
4. **Parameterize, don't duplicate**: If tests differ only in one constructor parameter, expose that parameter in the helper factory rather than creating separate duplicated calls.

---

## Exceptions and Special Cases

* **Entirely distinct widget configurations**: If each `testWidgets` block genuinely tests a different widget type or requires a significantly different tree structure, duplication is not a smell — each setup is intentional.
* **Single-test files**: If a test file contains only one or two `testWidgets` blocks, the duplication threshold is not reached and extraction may introduce unnecessary abstraction.
* **Framework configuration differences**: If tests require different `ThemeData`, `Locale`, or routing configurations that are central to what is being tested, those differences must remain explicit rather than hidden in a helper.

---

## Detection Tools

* **`dart analyze`**: Does not natively detect Widget Setup Smell, but custom lint rules can be defined via `custom_lint`.
* **Manual code review**: Effective heuristic — search for `pumpWidget` occurrences per file and assess whether a factory extraction would reduce repetition.

---

## Empirical Evidence

This smell was identified as a distinct Flutter-specific pattern during the **Validation Phase** of this research, through manual inspection of widget test files in real-world open-source Flutter projects. In the controlled validation dataset constructed for this study, 10 intentional positive instances were crafted, each containing at least 3 `testWidgets` blocks with identical `MaterialApp`/`Scaffold` wrapping. The pattern's prevalence in real projects was directly motivated by observations from repository mining, where widget test files averaging 200+ lines were found to contain 80%+ boilerplate duplication.

This smell is distinct from *General Fixture* (which concerns shared data initialization rather than widget tree construction) and is exclusive to the `flutter_test` ecosystem, making it a meaningful addition to a Dart/Flutter-specific test smell catalog.

---

## References and Related Studies

* Flutter Official Documentation: [Widget Testing](https://docs.flutter.dev/cookbook/testing/widget/introduction)
* Bavota, G. et al. (2015). *An Empirical Analysis of the Distribution of Unit Test Smells and Their Impact on Software Maintenance*. ICSM.
* Peruma, A. et al. (2020). *An Exploratory Characterization of Bad Testing Practices Using TsDetect*. EASE.
* Soares, G. et al. (2023). *A Multimethod Study on Test Smells*. JSS.

---

## Note

**Widget Setup Smell** is one of three test smells identified specifically for the Dart/Flutter ecosystem in this research, alongside *Expected Resolution Omission (ERO)* and *Residual State*. Its Flutter exclusivity stems from the framework's imperative widget tree construction model (`pumpWidget`), which does not have a direct analogue in Java/JUnit-based ecosystems where this class of duplication would manifest differently.
