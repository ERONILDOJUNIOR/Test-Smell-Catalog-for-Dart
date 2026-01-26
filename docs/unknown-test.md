# Unknown Test

## Description

**Unknown Test** occurs when a test method does not contain any assertions. As a result, the test is reported as *passed* as long as no exception is thrown during its execution. This creates a false sense of correctness, since no actual behavior or outcome is being validated.

Such tests fail to fulfill the fundamental purpose of testing: verifying that the system behaves as expected.

---

## Symptoms and Impact

The presence of **Unknown Test** may lead to:

- **False Confidence**: Tests appear to pass even though no conditions are being verified.
- **Test Suite Pollution**: Empty or ineffective tests add noise to the test suite, making it harder to understand what is actually being validated.
- **Reduced Test Value**: The test does not serve as executable documentation of system behavior.

---

## Identification Criteria

A test is likely to exhibit the **Unknown Test** smell if it meets one or more of the following conditions:

- The test contains **no assertions** (`expect`, `expectLater`, or equivalent).
- The test name or structure does not clearly indicate what behavior is being validated.
- The test executes production code but does not verify outputs, state changes, or observable effects.

---

## Code Examples

### Example with Unknown Test

```dart
import 'package:flutter_test/flutter_test.dart';

void main() {
  test("Calculate Discount Test", () {
    var cart = ShoppingCart();
    cart.addItem(Item(price: 100));

    // The method is executed, but no assertion is made
    var discount = cart.calculateDiscount();
  });
}

class ShoppingCart {
  List<Item> items = [];

  void addItem(Item item) {
    items.add(item);
  }

  double calculateDiscount() {
    double total = 0;

    for (var item in items) {
      total += item.price;
    }

    return total * 0.1; // 10% discount
  }
}

class Item {
  int price;

  Item({required this.price});
}
```

In this example, the test will always pass unless an exception is thrown, even if the discount calculation is incorrect.


### Example without Unknown Test

```dart
import 'package:flutter_test/flutter_test.dart';

void main() {
  test("Calculate Discount Test", () {
    var cart = ShoppingCart();
    cart.addItem(Item(price: 100));

    var discount = cart.calculateDiscount();

    expect(discount,10,reason: "Expected the discount to be 10% of the total price",
    );
  });
}
```

Here, the test explicitly verifies the expected behavior, making it meaningful and reliable.

---

## Recommended Refactorings

To eliminate **Unknown Test**, consider the following practices:

* **Always Assert Something**: Every test should contain at least one assertion that verifies behavior, state, or output.
* **Remove Useless Tests**: If a test does not validate anything and serves no purpose, it should be removed.
* **Review Tests Regularly**: During maintenance or refactoring, ensure that all tests meaningfully verify system behavior.

---

## Notes on `verify` and Mock-Based Tests

In the Dart testing ecosystem, particularly when using mocking frameworks such as `mockito`, the `verify` function is commonly used to check interactions with mocked dependencies (e.g., whether a method was called).

However, **for the purposes of this research and smell detection strategy**:

* Calls to `verify` are **not considered assertions**.
* A test that contains **only `verify` statements**, even if it verifies multiple interactions, is still classified as an **Unknown Test**.
* Only outcome-oriented assertions such as `expect` or `expectLater` are considered valid assertions.

This decision is intentional. While `verify` checks interaction behavior, it does not validate observable system outcomes or state. Relying solely on interaction verification can allow defects to go unnoticed, especially when the system produces incorrect results but still performs the expected calls.

Therefore:

> A test without `expect` or `expectLater` is considered an **Unknown Test**, regardless of the number of `verify` calls it contains.

---

## Final Remarks

**Unknown Test** represents one of the most critical test smells, as it provides no real validation while increasing the apparent size and confidence of the test suite. Eliminating this smell improves test reliability, clarity, and overall software quality.

---

## References

* Fowler, M. (1999). *Refactoring: Improving the Design of Existing Code*
* Meszaros, G. (2007). *xUnit Test Patterns: Refactoring Test Code*
* Van Deursen, A., et al. (2001). *Refactoring Test Code*
