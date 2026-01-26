# Magic Number Test

## Description

**Magic Number Test** occurs when assertions in a test method use numeric literals whose meaning is not explicitly clear. These values appear directly in assertions or test setup without being associated with named constants that explain their origin or intent.

Such “magic numbers” reduce test readability and comprehension, as developers are forced to infer the purpose of each value solely from the surrounding context.

---

## Symptoms and Impact

The presence of **Magic Number Test** may result in:

- **Reduced Readability**: The intent of the test becomes unclear, especially for developers unfamiliar with the domain.
- **Higher Maintenance Cost**: When expected values change, multiple hard-coded numbers must be updated, increasing the risk of inconsistencies.
- **Lower Test Expressiveness**: Tests lose their role as clear, executable documentation of system behavior.

---

## Identification Criteria

A test is likely to exhibit the **Magic Number Test** smell if it meets one or more of the following conditions:

- Numeric literals are used directly in assertions without descriptive constants.
- The same numeric values appear multiple times in a test without explanation.
- The meaning or origin of a numeric value is not evident from the test name or structure.

---

## Code Examples

### Example with Magic Number Test

```dart
import 'package:test/test.dart';

void main() {
  test('Calculate discount with magic numbers', () {
    final discount = calculateDiscount(150); // Magic number
    
    expect(discount, equals(15)); // Magic number
  });
}

double calculateDiscount(int basePrice) {
  return basePrice * 0.1;
}
```

In this example, the values `150` and `15` have implicit meaning that must be inferred, reducing clarity and maintainability.

---

### Example without Magic Number Test

```dart
import 'package:test/test.dart';

void main() {
  test('Calculate discount without magic numbers', () {
    const int basePrice = 150;
    const int expectedDiscount = 15;

    final discount = calculateDiscount(basePrice);

    expect(
      discount,
      equals(expectedDiscount),
      reason: 'Expected a 10% discount for a base price of 150',
    );
  });
}

double calculateDiscount(int basePrice) {
  const double discountRate = 0.1;
  return basePrice * discountRate;
}
```

By introducing named constants, the test clearly communicates the intent and meaning of each value.

---

## Recommended Refactorings

To eliminate **Magic Number Test**, the following practices are recommended:

* **Replace Literals with Named Constants**: Use constants with descriptive names to clarify the meaning of numeric values.
* **Align Constants with Domain Concepts**: Whenever possible, reflect business rules or domain terminology in constant names.
* **Document Non-Obvious Values**: When a number represents a specific rule or threshold, provide additional context through assertion messages or comments.

---

## Exceptions and Special Cases

In trivial cases where a numeric value is universally understood and carries no special meaning (for example, `0` for initialization or `1` for a single occurrence), the direct use of numeric literals may be acceptable. However, any value representing a business rule, threshold, or expected outcome should be explicitly named.

---

## Final Remarks

Removing magic numbers from tests improves clarity, expressiveness, and long-term maintainability. Well-named constants allow tests to serve as precise and reliable documentation of expected system behavior.

---

## References

* Fowler, M. (1999). *Refactoring: Improving the Design of Existing Code*
* Meszaros, G. (2007). *xUnit Test Patterns: Refactoring Test Code*
* Martin, R. C. (2008). *Clean Code: A Handbook of Agile Software Craftsmanship*
