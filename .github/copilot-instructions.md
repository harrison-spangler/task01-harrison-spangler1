# AI Coding Agent Instructions - cs345-task01

## Project Overview
This is a **Java banking system** (CS 345 academic project at Ithaca College) implementing a `BankAccount` class with core operations: account creation with email validation, balance retrieval, and withdrawals with exception handling.

## Architecture & Key Components

### Core Domain Model
- **`BankAccount`** ([src/main/java/edu/ithaca/dturnbull/bank/BankAccount.java](src/main/java/edu/ithaca/dturnbull/bank/BankAccount.java)): Single immutable-email, mutable-balance account class
  - State: `email` (String), `balance` (double)
  - Critical: Email must be validated on construction; invalid emails throw `IllegalArgumentException`
  - Withdrawal enforces balance constraint; insufficient funds throw `InsufficientFundsException`

- **`InsufficientFundsException`** ([src/main/java/edu/ithaca/dturnbull/bank/InsufficientFundsException.java](src/main/java/edu/ithaca/dturnbull/bank/InsufficientFundsException.java)): Custom checked exception for withdraw failures

### Architecture Pattern
Simple object-oriented design with:
- Constructor validation (fail-fast) for invalid state
- Checked exception pattern for business rule violations (not null validation errors)
- Static utility method for email validation (reusable, testable)

## Build & Test Workflow

### Build with Maven
```bash
mvn clean compile          # Compile only
mvn clean package          # Compile + test + package
mvn clean install          # Build all + install to local repo
```

### Run Tests
```bash
mvn test                   # Run all JUnit 5 tests in src/test/
mvn test -Dtest=BankAccountTest  # Run specific test class
```

**Test Framework**: JUnit 5 (Jupiter API) with assertions library
- Tests use `@Test` annotation, assertion methods like `assertEquals()`, `assertTrue()`, `assertThrows()`
- All tests in [src/test/java/edu/ithaca/dturnbull/bank/BankAccountTest.java](src/test/java/edu/ithaca/dturnbull/bank/BankAccountTest.java) exercise constructor, getters, and withdraw logic

## Project-Specific Conventions

### Email Validation Pattern
The project uses a **minimal email validator** in `BankAccount.isEmailValid()`:
- Accepts any string containing `@` symbol (even "a@b.com" is valid; "" is invalid)
- Not RFC-compliant; reflects scope of academic exercise
- Static method allows validation before object construction
- **Do not replace with stricter regex patterns** unless explicitly requested—this constraint is intentional for the assignment

### Exception Handling Philosophy
- **Constructor**: Throws `IllegalArgumentException` for invalid inputs (email validation) → prevents invalid object creation
- **Business Operations**: Throws `InsufficientFundsException` (checked exception) for rule violations (insufficient balance) → caller must handle
- This asymmetry (unchecked vs. checked) reflects the design: construction failures are programming errors; operation failures are expected scenarios

### Floating-Point Precision
- Balance uses `double` type
- Test assertions use `0.001` delta tolerance (e.g., `assertEquals(200, balance, 0.001)`)
- Follow this pattern when adding new assertions; do not use exact `==` comparisons for doubles

### Immutable Email, Mutable Balance
- Email cannot be changed after construction (no setter)
- Balance is mutable only via `withdraw()` method (not direct field access)
- Preserve this encapsulation pattern if adding new operations (e.g., `deposit()`)

## Development Checkpoints

### When Adding Features
1. **Preserve immutability contract** for email field
2. **Use checked exceptions** (like `InsufficientFundsException`) for expected business rule failures
3. **Add test cases** following the existing `@Test` pattern in `BankAccountTest`
4. **Maintain email validation** as first constructor step; throw `IllegalArgumentException` immediately if invalid
5. **Run `mvn test`** to validate all tests pass before committing

### When Modifying BankAccount
- All public method changes require corresponding test updates in `BankAccountTest`
- Avoid changing static method signatures (e.g., `isEmailValid()`) without updating tests

## Java & Maven Details
- **Java Version**: 17 (source and target)
- **Maven Plugins**: Minimal setup (maven-compiler-plugin only)
- **Package Structure**: `edu.ithaca.dturnbull.bank` (follow for new classes)
- **Dependencies**: JUnit 5 only (test scope)

## File Reference Summary
| File | Purpose |
|------|---------|
| `pom.xml` | Maven build config, Java 17, JUnit 5 dependency |
| `BankAccount.java` | Core domain class—email validation, balance, withdraw |
| `InsufficientFundsException.java` | Checked exception for insufficient funds |
| `BankAccountTest.java` | Comprehensive test suite (constructor, getters, withdraw, email validation) |
