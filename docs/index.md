# Test Smells Catalog for Dart and Flutter

This document presents a **Test Smells Catalog for the Dart and Flutter ecosystem**, aiming to support researchers, software developers, and quality assurance professionals in the identification, analysis, and mitigation of recurring problems in automated tests.

The catalog is grounded in established literature on *test smells* and systematically adapted to the Dart and Flutter context, considering language-specific characteristics, architectural patterns, and framework conventions.

---

## 1. Introduction

### 1.1 Test Smells

**Test Smells** are indicators of structural or design problems in automated test code that, although not necessarily causing immediate failures, **negatively affect test readability, reliability, maintainability, and long-term effectiveness**.

Analogous to *code smells* in production code, test smells serve as warning signs of potential weaknesses in test design, which may compromise software evolution and confidence in test results.

---

### 1.2 Motivation

While test smells have been extensively documented in languages such as Java and frameworks like JUnit, **there is a lack of systematized and domain-specific material for the Dart and Flutter ecosystem**.

This catalog addresses this gap by providing a structured, technically rigorous reference tailored to Dart and Flutter testing practices.

---

## 2. Objectives

The primary objectives of this catalog are to:

- Systematize relevant test smells in the context of Dart and Flutter;
- Provide **precise and well-founded definitions** for each test smell;
- Establish **clear identification criteria**, supported by code examples;
- Present **refactoring and mitigation strategies** based on best practices;
- Analyze the **impact of test smells on test quality and software maintainability**.

---

## 3. Catalog Structure

Each test smell in this catalog is documented using a standardized structure consisting of:

- **Test Smell Name**
- **Conceptual Description**
- **Symptoms and Impact**
- **Identification Criteria**
- **Code Examples in Dart/Flutter**
- **Refactoring or Mitigation Strategies**
- **Notes and Exceptions**, when applicable

This structure ensures consistency, clarity, and comparability across catalog entries.

---

## 4. Usage Guidelines

The following approach is recommended when using this catalog:

1. Navigate the index to locate relevant test smells;
2. Compare the identification criteria with the test code under analysis;
3. Assess the described potential impacts;
4. Apply the suggested refactoring or mitigation strategies;
5. Use the catalog as a continuous reference during test development and review activities.

---

## 5. Test Smells Index

1. [Assertion Roulette](assertion-roulette.md)  
2. [Conditional Test Logic](conditional-test-logic.md)  
3. [Constructor Initialization](constructor-initialization.md)  
4. [Default Test](default-test.md)  
5. [Dependent Test](dependent-test.md)  
6. [Duplicate Assert](duplicate-assert.md)  
7. [Eager Test](eager-test.md)  
8. [Empty Test](empty-test.md)  
9. [Exception Handling](exception-handling.md)  
10. [General Fixture](general-fixture.md)  
11. [Ignored Test](ignored-test.md)  
12. [Lazy Test](lazy-test.md)  
13. [Magic Number Test](magic-number-test.md)  
14. [Mystery Guest](mystery-guest.md)  
15. [Redundant Print](redundant-print.md)  
16. [Redundant Assertion](redundant-assertion.md)  
17. [Resource Optimism](resource-optimism.md)  
18. [Sensitive Equality](sensitive-equality.md)  
19. [Sleepy Test](sleepy-test.md)  
20. [Unknown Test](unknown-test.md)  
21. [Verbose Test](verbose-test.md)

### Dart/Flutter-Specific Smells *(identified during empirical and validation phases)*

22. [Expected Resolution Omission (ERO)](expected-resolution-omission.md)  
23. [Residual State](residual-state.md)  
24. [Widget Setup Smell](widget-setup.md)

---

## 6. Contributions

This catalog is a living artifact. Contributions are encouraged, particularly in the following areas:

- Identification of test smells specific to Dart and Flutter;
- Conceptual refinement of existing definitions;
- Inclusion of empirical or real-world examples;
- Discussion of limitations, trade-offs, and acceptable exceptions.

Contributions may be submitted via issues or pull requests.

---

## 7. Final Remarks

This catalog is intended to support **research, education, and professional practice** in software testing within the Dart and Flutter ecosystem.

It does not replace technical judgment but provides a structured foundation for critical analysis and continuous improvement of automated tests.

---
