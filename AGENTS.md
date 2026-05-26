# AGENTS.md

This file provides guidance to AI coding assistants (GitHub Copilot, Cursor, Warp, etc.) when working with code in this repository. It is the standard way to give an AI agent the context it needs to understand the project without having to explore every file from scratch.

## Stack and runtime constraints
- Java 21 (project is configured with `<source>/<target>` and `maven.compiler.release` set to 21 in `pom.xml`)
- Maven is the build/test tool
- JUnit Jupiter + Mockito are used for tests

## Common commands
- Compile:
  - `mvn compile`
- Clean + compile (common CI check):
  - `mvn clean compile`
- Run all tests:
  - `mvn test`
  - `mvn clean test`
- Run a single test class:
  - `mvn -Dtest=CalculatorTest test`
- Run a single test method:
  - `mvn -Dtest=CalculatorTest#givenTwoPositiveNumbers_whenAdding_thenShouldReturnTheirSum test`
- Build JAR:
  - `mvn package`
- Build JAR with clean:
  - `mvn clean package`
- Run app from packaged JAR:
  - `java -jar target/calculator-practice1-1.0.0.jar`
- Run app in command mode:
  - `java -jar target/calculator-practice1-1.0.0.jar add 5 3`

## High-level architecture
- The codebase is intentionally small and split into two production classes under `src/main/java/es/uma/iyps/calculator/`:
  - `Calculator`: domain/core logic for arithmetic and number operations.
  - `CalculatorApp`: CLI entrypoint and input orchestration.
- `Calculator` is state-free and exposes pure operations (`add`, `subtract`, `multiply`, `divide`, `power`, `squareRoot`, `factorial`, `isPrime`) with argument validation via `IllegalArgumentException` for invalid inputs.
- `CalculatorApp` owns user interaction and delegates all computation to a single `Calculator` instance:
  - Interactive mode (`showMenu`) when no CLI args are passed.
  - Argument mode (`processArguments`) when operation/operands are passed in the command line.
- Packaging is configured so the JAR is executable with `es.uma.iyps.calculator.CalculatorApp` as `Main-Class` (see Maven JAR plugin config in `pom.xml`).

## Test architecture
- `src/test/java/es/uma/iyps/calculator/CalculatorTest.java`:
  - Main behavioral test suite, organized with JUnit nested classes:
    - basic operations
    - advanced operations
    - prime checks
    - integration scenarios combining operations
  - Uses parameterized tests heavily (`@CsvSource`, `@ValueSource`).
- `src/test/java/es/uma/iyps/calculator/CalculatorMockitoTest.java`:
  - Demonstrates mocking/spying patterns and interaction verification with Mockito.
  - Includes an inner `CalculatorService` class used only for mock-based testing examples.

## Editing guidance for future changes
- If you add or change calculator operations, update both:
  - core behavior in `Calculator`
  - CLI wiring in `CalculatorApp` (interactive switch + argument parser) if the operation should be user-accessible.
- Keep exception messages stable when possible; tests assert exact message text for invalid-input cases.
