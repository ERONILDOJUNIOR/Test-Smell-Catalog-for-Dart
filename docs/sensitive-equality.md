# Sensitive Equality

## Description

**Sensitive Equality** occurs when a test relies heavily on exact value comparisons, such as strings or numbers with specific formats, without accounting for minor variations that may be acceptable. This type of test is prone to frequent failures due to trivial differences that do not affect the functional behavior of the code, such as differences in capitalization, whitespace, or decimal precision.

---

## Symptoms and Impact

* **Unnecessary Failures**: Tests may fail due to minor variations in data, which are irrelevant to the behavior the test intends to validate.
* **Low Flexibility**: Tests that use sensitive equality are harder to maintain, especially if the system behavior changes slightly.
* **Inconsistent Results**: Can lead to intermittent failures when output slightly varies between executions, complicating debugging.

---

## Identification Criteria

To identify **Sensitive Equality**, look for:

* Tests that directly compare strings, numbers, or lists without tolerance for insignificant variations.
* Rigid comparisons that fail due to differences in capitalization, spacing, or numeric precision.

---

## Example Code

### Example with Sensitive Equality

```dart
import 'package:test/test.dart';

class Point {
  final int x;
  final int y;

  Point(this.x, this.y);

  @override
  String toString() => 'Point(x: $x, y: $y)';
}

void main() {
  test('Sensitive Equality using toString', () {
    final point = Point(1, 2);
    // This test will fail if formatting changes, e.g., extra space or different toString
    expect(point.toString(), equals('Point(x: 1, y: 2)'));
  });
}
```

Problem: This test is highly sensitive to formatting changes in toString(). Any small modification (like spacing or apitalization) will make the test fail, even though the object’s data is correct.

### Example without Sensitive Equality

```dart
import 'package:test/test.dart';

class Point {
  final int x;
  final int y;

  Point(this.x, this.y);

  @override
  String toString() => 'Point(x: $x, y: $y)';
}

void main() {
  test('Insensitive Equality using object properties', () {
    final point = Point(1, 2);
    // Compare the actual data instead of string representation
    expect(point.x, equals(1));
    expect(point.y, equals(2));
  });
}

```
Solution: Compare the object properties directly, not the string representation. This makes the test resilient to formatting changes in toString().

---

## Recommended Fixes

To mitigate **Sensitive Equality**:

* **Use Insensitive Comparisons**: Normalize values before comparison, e.g., `toLowerCase()` for strings or rounding for numbers.
* **Apply Tolerances**: When comparing numeric values, allow an acceptable margin of error to prevent failures caused by minor precision differences.
* **Flexible Assertions**: Instead of strict equality, validate the presence of key elements while permitting minor variations.

---

## Exceptions and Special Cases

In certain contexts, such as testing protocol-specific values, exact comparisons may be necessary. In these cases, document the test explicitly to indicate why strict equality is required.

---

## References

* Fowler, M. (1999). *Refactoring: Improving the Design of Existing Code*.
* Meszaros, G. (2007). *xUnit Test Patterns: Refactoring Test Code*.
* Van Deursen, A., et al. (2001). "Refactoring Test Code."

---

## Note

Avoiding **Sensitive Equality** increases test resilience, ensuring failures occur only for meaningful differences and reducing the occurrence of false positives.
