# Conditional Test Logic

## Description

**Conditional Test Logic** occurs when test methods contain control-flow constructs, such as `if`, `switch`, `for`, `while`, or `forEach`, that influence how the test is executed. The presence of such logic introduces multiple execution paths into a single test, reducing determinism and making failures harder to diagnose.

In a well-designed test suite, each test case should represent a single, explicit scenario and follow a linear execution path. Embedding decision-making or iteration logic within tests violates this principle and weakens the role of tests as precise and executable specifications.

---

## Symptoms and Impact

The presence of **Conditional Test Logic** may lead to the following issues:

- **Reduced Predictability**: Test behavior may vary depending on runtime conditions, making results harder to reason about.
- **Lower Diagnostic Quality**: When a test fails, it may be unclear which execution path was taken or which condition triggered the failure.
- **Increased Maintenance Effort**: Conditional and iterative constructs increase cognitive complexity, making tests harder to read, review, and evolve.
- **Hidden Coverage Gaps**: Certain scenarios in the production code may not be exercised if conditional paths in the test are not executed.

---

## Identification Criteria

A test is likely to exhibit **Conditional Test Logic** if it contains one or more of the following within the test body:

- Conditional statements such as `if`, `else`, or `switch`.
- Iterative constructs such as `for`, `while`, or `forEach`.
- Assertions whose execution depends on runtime-evaluated conditions rather than a fixed and explicit test scenario.

---

## Code Examples

### Example with Conditional Test Logic

```dart
import 'package:flutter_test/flutter_test.dart';

class ShoppingCart {
  final double totalAmount;
  double discount = 0;

  ShoppingCart(this.totalAmount);

  void applyDiscount() {
    if (totalAmount < 0) {
      throw ArgumentError('Amount cannot be negative');
    }
    discount = totalAmount > 100 ? 0.1 : 0;
  }
}

void main() {
  test('Calculates discount using conditional logic in the test', () {
    final cart = ShoppingCart(50);
    cart.applyDiscount();

    if (cart.totalAmount > 100) {
      expect(cart.discount, 0.1);
    } else {
      expect(cart.discount, 0);
    }
  });
}
```

In this example, the test outcome depends on a conditional branch inside the test itself. As a result, the intent of the test is ambiguous and the behavior under failure is less predictable.


### Example without Conditional Test Logic

```dart
import 'package:flutter_test/flutter_test.dart';

void main() {
  test('Applies a 10% discount for totals above 100', () {
    final cart = ShoppingCart(150);
    cart.applyDiscount();

    expect(
      cart.discount,
      0.1,
      reason: 'A 10% discount is expected for totals above 100',
    );
  });

  test('Applies no discount for totals equal to or below 100', () {
    final cart = ShoppingCart(50);
    cart.applyDiscount();

    expect(
      cart.discount,
      0,
      reason: 'No discount is expected for totals equal to or below 100',
    );
  });
}

class ShoppingCart {
  final double totalAmount;
  double discount = 0;

  ShoppingCart(this.totalAmount);

  void applyDiscount() {
    if (totalAmount < 0) {
      throw ArgumentError('Amount cannot be negative');
    }
    discount = totalAmount > 100 ? 0.1 : 0;
  }
}
```

Each test represents a single, explicit scenario, eliminating conditional logic from the test body.

---

## Iterative Constructs and `forEach` as a Smell

Iterative constructs such as `for`, `while`, and especially `forEach` are commonly found in test code. Their presence within tests is a strong indicator of **Conditional Test Logic**.

When assertions are executed inside loops, the test implicitly validates multiple scenarios within a single test case. This practice reduces clarity and significantly weakens failure diagnostics.

### Example: Assertions Inside a `forEach`

```dart
// Source: https://stackoverflow.com/q
// Posted by jsa
// Retrieved 2026-01-25
// License: CC BY-SA 4.0

import 'package:test/test.dart';

void main() {
  test('formatDay should format dates correctly', () async {
    var inputsToExpected = {
      DateTime(2018, 11, 01): 'Thu 1',
      // ...
      DateTime(2018, 11, 07): 'Wed 7',
      DateTime(2018, 11, 30): 'Fri 30',
    };

    var inputsToResults = inputsToExpected.map(
      (input, expected) => MapEntry(input, formatDay(input)),
    );

    inputsToExpected.forEach((input, expected) {
      expect(inputsToResults[input], equals(expected));
    });
  });
}
```

If a failure occurs in this test, the output does not clearly indicate **which input value** or **which iteration** caused the failure. As a result, developers must manually inspect the test data to identify the root cause.

---

## Recommended Refactorings

To mitigate **Conditional Test Logic**, the following practices are recommended:

* **Remove Conditional and Iterative Logic from Tests**: Replace them with multiple, focused test cases, each covering a single scenario.
* **Avoid Assertions Inside Loops**: Prefer explicit tests over loops that perform multiple assertions.
* **Keep Tests Linear and Deterministic**: Each test should follow a single execution path and validate one well-defined behavior.
* **Control Variability via Test Setup**: Use fixed inputs, fixtures, or mocks to define scenarios explicitly, rather than relying on runtime conditions.

---

## Exceptions and Special Cases

In rare cases where the testing framework provides explicit and well-diagnosed test parameterization mechanisms, iterative constructs may be acceptable. However, such mechanisms are not natively emphasized in `package:test` or `flutter_test`, and their use should be carefully justified.

---

## Notes

**Conditional Test Logic** weakens tests as executable specifications. By embedding decision-making or iteration logic within tests, developers obscure intent, reduce diagnosability, and increase maintenance effort. Eliminating this smell leads to clearer, more robust, and more maintainable test suites.

---

## References

* Fowler, M. (1999). *Refactoring: Improving the Design of Existing Code*.
* Meszaros, G. (2007). *xUnit Test Patterns: Refactoring Test Code*.
* Van Deursen, A., et al. (2001). *Refactoring Test Code*.
