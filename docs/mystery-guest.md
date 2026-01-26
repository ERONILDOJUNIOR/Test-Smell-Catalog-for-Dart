# Mystery Guest

## Description

**Mystery Guest** occurs when a test depends on external data or state—such as global configuration, environment settings, or external databases—that is not explicitly visible within the test itself. This creates an implicit dependency and makes the test behavior harder to understand, as critical information required for the test’s execution and success is hidden outside the test code.

In other words, **Mystery Guest** acts as a “hidden participant” in the test, since the external values it relies on are not clearly defined in the test context, complicating comprehension and maintenance.

---

## Symptoms and Impact

* **Debugging Difficulty**: Tests fail or behave inconsistently due to hidden external dependencies or implicit state.
* **Configuration Complexity**: Other developers may struggle to reproduce the exact test environment when external configuration is required but not documented.
* **Reduced Reliability**: Tests become less reliable because their outcomes depend on factors outside their immediate scope.

---

## Identification Criteria

To identify **Mystery Guest**, look for:

* Tests that rely on external state or data, such as global variables, configuration files, or database records.
* Tests that require specific environment setup or external dependencies to pass, without making those dependencies explicit in the test code.

---

## Example Code

### Example with Mystery Guest

```dart
import 'package:test/test.dart';

void main() {
  test('User Profile with Mystery Guest', () {
    final userProfile = fetchUserProfile(); // Depends on externally configured data
    expect(userProfile.name, equals("Alice"));
  });
}

UserProfile fetchUserProfile() {
  // Simulates retrieving a user profile from an external database
  // Assumes that a user named "Alice" already exists
  return UserProfile(name: "Alice");
}

class UserProfile {
  final String name;
  UserProfile({required this.name});
}
```

**Problem:**
The test implicitly assumes that a specific user exists in an external system. This dependency is not visible in the test, making it harder to understand and reproduce.

---

### Example without Mystery Guest

```dart
import 'package:test/test.dart';

void main() {
  test('User Profile without Mystery Guest', () {
    final testUser = User(id: 1, name: "Alice");
    final userProfile = fetchUserProfile(testUser.id);

    expect(
      userProfile.name,
      equals(testUser.name),
      reason: "Expected user name to match the explicitly defined test user",
    );
  });
}

UserProfile fetchUserProfile(int userId) {
  // Returns a deterministic, test-specific user profile
  return UserProfile(id: userId, name: "Alice");
}

class User {
  final int id;
  final String name;
  User({required this.id, required this.name});
}

class UserProfile {
  final int id;
  final String name;
  UserProfile({required this.id, required this.name});
}
```

**Solution:**
Define all required data explicitly within the test or provide it through controlled test inputs. This removes hidden dependencies and improves test clarity.

---

## Recommended Fixes

To mitigate **Mystery Guest**:

* **Use Test-Specific Data or Mocks**: Explicitly define required data inside the test to avoid hidden dependencies.
* **Isolate the Test Environment**: Ensure tests do not rely on production data, global state, or external configuration.
* **Document Dependencies Explicitly**: If an external dependency is unavoidable, clearly document it within the test setup.

---

## Exceptions and Special Cases

In integration or acceptance testing scenarios, using real data or external dependencies may be acceptable. Even in these cases, dependencies should be isolated, well documented, and clearly distinguished from unit tests.

---

## Note

Avoiding **Mystery Guest** improves test transparency, reliability, and maintainability. Tests should be self-contained and explicit about all data and state they require.

---

## References

* Fowler, M. (1999). *Refactoring: Improving the Design of Existing Code*.
* Meszaros, G. (2007). *xUnit Test Patterns: Refactoring Test Code*.
* Van Deursen, A., et al. (2001). "Refactoring Test Code."