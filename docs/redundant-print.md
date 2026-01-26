# Redundant Print

## Description

**Redundant Print** occurs when a test method contains print statements. While `print` can be useful for debugging, excessive or permanent use can consume computational resources, increase execution time (especially if long-running methods are called within the print statement), and make test results harder to read and interpret.

Persistent prints can clutter the console output and reduce the clarity of test feedback.

---

## Symptoms and Impact

* **Cluttered Console Output**: Prints make the console output confusing and difficult to read.
* **Poor Practice**: Reliance on `print` instead of assertions or other automated tools that provide immediate and precise feedback about test success or failure.

---

## Identification Criteria

To identify **Redundant Print**, look for:

* Tests containing multiple `print` statements without a valid reason.

---

## Example Code

### Example with Redundant Print

```dart
import 'package:test/test.dart';

void main() {
  test('Redundant Print Test', () {
    final cart = ShoppingCart();
    cart.add(Item(price: 10));
    cart.add(Item(price: 20));
  
    print("Total Price: ${cart.getTotalItems()}");
    print("Total Items: ${cart.getTotalItems()}");
  });
}
```

**Problem:** The test relies on prints instead of assertions. This approach does not automatically verify correctness and clutters the console.

---

### Example without Redundant Print

```dart
import 'package:test/test.dart';

void main() {
  test('Calculate Total without Redundant Print', () {
    final cart = ShoppingCart();
    cart.add(Item(price: 10));
    cart.add(Item(price: 20));

    expect(cart.getTotalPrice(), equals(30), reason: "Total price should be 30 after adding items");
    expect(cart.getTotalItems(), equals(2), reason: "Total items should be 2 after adding items");
  });
}
```

**Solution:** Replace prints with assertions that directly verify the expected conditions. This ensures tests are automated, deterministic, and readable.

---

## Recommended Fixes

To mitigate **Redundant Print**:

* **Replace `print` with Assertions**: Use assertions to check conditions directly instead of printing values.
* **Remove Unnecessary Prints**: Eliminate prints that do not contribute to debugging or verification.
* **Use Controlled Logging**: When extra output is necessary for debugging, implement a logging solution that can be enabled or disabled as needed, preventing excessive prints in tests.

---

## Exceptions and Special Cases

During initial development or complex debugging scenarios, temporary prints may be acceptable. However, they should be removed before finalizing the test.

---

## Note

Eliminating **Redundant Print** from tests helps maintain a clean output, focused on relevant information, and increases the clarity and reliability of the feedback provided by tests.

---

## References

* Fowler, M. (1999). *Refactoring: Improving the Design of Existing Code*.
* Meszaros, G. (2007). *xUnit Test Patterns: Refactoring Test Code*.
* Van Deursen, A., et al. (2001). "Refactoring Test Code."
