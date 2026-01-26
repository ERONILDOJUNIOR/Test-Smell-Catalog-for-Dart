## Dependent Test – State Sharing via Global Variables

### Description

A **Dependent Test** also occurs when tests **share mutable global state** and rely on implicit execution order to pass. In this scenario, the outcome of a test depends on whether a previous test has modified a shared variable and whether that state has been properly reset.

This form of dependency is especially dangerous because the test framework may execute tests in a different order, in parallel, or selectively, causing tests to fail intermittently or produce misleading results.

---

## Identified Dependent Test Patterns

The following patterns are **explicitly considered Dependent Test smells** in this catalog.

### 1. Shared Global Variables Without Reset

Any **mutable global variable** (`int`, `String`, `List`, `Map`, `bool`, `double`, etc.) that is:

* Modified in one test, and
* Read or modified in another test, and
* **Not reset in `setUp()` or `tearDown()`**

creates an execution-order dependency.

This includes primitive types and collections.

#### Why this is a smell

* Tests become order-dependent.
* Running a single test in isolation may fail.
* Running the full suite may pass or fail depending on execution order.
* Parallel execution becomes unsafe.

---

### 2. Multiple Tests Writing and Reading the Same Global State

When **two or more tests interact with the same global variable**, the dependency grows with each additional test.

#### Example Patterns (Conceptual)

* Test A increments a global counter

* Test B assumes the counter starts at zero

* Test A sets a message

* Test B expects the message to be empty

* Test A adds items to a shared list

* Test B checks the list size

* Test C expects the list to be empty

Each additional test increases the **dependency chain**, multiplying the number of Dependent Test smells.

---

### 3. Collections Shared Across Tests

Shared collections (`List`, `Map`, `Set`) are particularly problematic.

They are considered **Dependent Test smells** when:

* Items are added or removed in one test
* Another test assumes a specific collection state
* No reset is performed between tests

This includes cases where:

* One test fills a cache
* Another test reads from the cache
* Another test assumes the cache is empty

Even if the expected value matches, the test is still dependent.

---

### 4. Boolean Flags Used Across Tests

Boolean flags used to represent state transitions are also a source of dependency.

A smell exists when:

* One test sets a flag to `true`
* Another test assumes the flag is already `true`
* A later test sets it back to `false`

This creates **implicit sequencing requirements**, which violate test independence.

---

### 5. Numeric Values Modified Incrementally

Numeric globals (`int`, `double`) used in arithmetic across tests are Dependent Tests when:

* One test assigns or modifies the value
* Another test performs calculations based on the current value
* A later test expects the initial default value

These tests cannot be executed independently or reordered safely.

---

## What Is NOT Considered a Dependent Test

The following cases are **explicitly excluded** from Dependent Test detection:

### 1. Global Variables Reset in `setUp()`

If a global variable is **fully reset before each test**, it does **not** create a dependency.

Example characteristics:

* Reset happens in `setUp()`
* All tests start from the same known state
* No test relies on side effects from previous tests

---

### 2. Local Variables Inside Tests

Variables declared **inside a test body** are always safe.

They:

* Exist only within the test scope
* Cannot be accessed by other tests
* Do not introduce shared mutable state

---

### 3. Immutable or Recreated Test Data

Objects or collections created fresh inside each test do not introduce dependencies, even if they contain similar data.

---

## Summary of Detection Rules

A test **must be flagged as a Dependent Test** if:

* It reads or writes **mutable global state**, and
* That state is accessed by **another test**, and
* There is **no reset mechanism** ensuring isolation

The **number of Dependent Test smells increases** with the number of tests interacting with the same shared state.

---

## Note

This catalog treats **state-based dependencies** as first-class Dependent Test smells, even when:

* Tests pass consistently in a local environment
* The dependency is not immediately obvious
* The shared value happens to match the expected result

The key criterion is **test independence**, not execution success.
