# ADR-012: Playwright BDD Testing Architecture

## Status

Accepted

## Date

2026-01-17

## Context

PactoSigna requires end-to-end testing to validate critical user journeys, particularly for Part 11 compliance features like electronic signatures and audit logging. The testing approach must:

1. **Align with domain language** - Tests should use the ubiquitous language defined in `docs/domain/ubiquitous-language.md`
2. **Map to user stories** - Tests should directly trace to requirements in `docs/requirements/`
3. **Support compliance validation** - Audit log assertions are essential for Part 11 verification
4. **Enable agent-assisted test generation** - AI agents should be able to generate and maintain tests

### Options Considered

| Option                          | Description                 | Pros                                                       | Cons                                               |
| ------------------------------- | --------------------------- | ---------------------------------------------------------- | -------------------------------------------------- |
| **A) playwright-bdd**           | Native Playwright + Gherkin | Single test runner, direct integration, active maintenance | Newer package, smaller community                   |
| **B) Cucumber.js + Playwright** | Industry-standard BDD       | Large ecosystem, proven patterns                           | Two runners, complex configuration                 |
| **C) Native test.step()**       | BDD via code conventions    | Zero dependencies, simple                                  | No Gherkin files, less readable for non-developers |

### Environment Options

| Option                    | Description                      | Trade-off                               |
| ------------------------- | -------------------------------- | --------------------------------------- |
| **A) Firebase Emulators** | Full local emulation             | Deterministic, isolated, no cloud costs |
| **B) Test Project**       | Dedicated Firebase project       | Real behavior, but shared state issues  |
| **C) Hybrid**             | Emulators local, test project CI | Complexity in configuration             |

## Decision

We will use **playwright-bdd** with **Firebase Emulator Suite** for E2E testing.

### Credential Handling Strategy

E2E tests run **exclusively against Firebase Emulators**. Test credentials are:

1. **Not secrets** - Hardcoded in `e2e/fixtures/test-data.ts`
2. **Seeded fresh each run** - Emulators are cleared and re-seeded
3. **Abstracted via semantic steps** - Feature files use steps like "the test member" rather than explicit emails/passwords
4. **Explicit only when meaningful** - Specific values (SQL injection, special chars) only when testing that value

For production environment smoke testing, use a separate `smoke-tests/` suite with environment variables.

### Rationale

1. **playwright-bdd alignment**: Our user stories already follow Given/When/Then patterns, making Gherkin a natural fit
2. **Single toolchain**: Avoids complexity of managing Cucumber.js separately from Playwright
3. **Emulator determinism**: Part 11 compliance requires verifiable audit logs - emulators allow seeding known state and asserting exact outcomes
4. **Agent compatibility**: Gherkin features can be generated from requirements docs by AI agents

## Consequences

### Positive

- Tests are readable by non-developers (QA, regulatory)
- Direct traceability from requirements to test scenarios
- Deterministic test execution with emulator seeding
- Consistent with existing Vitest unit test patterns

### Negative

- Team must learn Gherkin syntax
- Emulator setup required in CI (additional configuration)
- playwright-bdd has smaller community than Cucumber.js

### Neutral

- Feature files become part of documentation
- Step definitions require maintenance as domain evolves

## Implementation

See [docs/testing/e2e-architecture.md](../testing/e2e-architecture.md) for detailed implementation guide.

## References

- [playwright-bdd documentation](https://vitalets.github.io/playwright-bdd/)
- [Firebase Emulator Suite](https://firebase.google.com/docs/emulator-suite)
- [Ubiquitous Language](../domain/ubiquitous-language.md)
- [Requirements Index](../requirements/index.md)
