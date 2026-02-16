# Testing Documentation

This directory contains testing strategy and architecture documentation for PactoSigna.

## Documents

| Document                                | Description                                                               |
| --------------------------------------- | ------------------------------------------------------------------------- |
| [E2E Architecture](e2e-architecture.md) | Playwright BDD testing architecture, directory structure, and conventions |
| [Best Practices](best-practices.md)     | Testing anti-patterns, MUI transitions, external service mocking          |

## Related Resources

### Architecture Decisions

- [ADR-012: Playwright BDD Testing](../architecture/adr-012-playwright-bdd-testing.md)
- [ADR-016: External Service Abstraction](../architecture/adr-016-external-service-abstraction.md)

### Agent Instructions

- [Automated Testing Agent](../../.claude/agents/quality-automated-testing.md)
- [Manual Testing Agent](../../.claude/agents/quality-manual-testing.md)

### Domain Context

- [Ubiquitous Language](../domain/ubiquitous-language.md) - Required terminology for tests
- [Bounded Contexts](../domain/bounded-contexts.md) - Test organization by context
- [Requirements Index](../requirements/index.md) - User stories to test

## Testing Strategy Overview

### Test Pyramid

```
        ╱╲
       ╱  ╲  E2E (Playwright BDD)
      ╱────╲  - Critical user journeys
     ╱      ╲  - Part 11 compliance flows
    ╱────────╲
   ╱          ╲  Integration (Vitest)
  ╱────────────╲  - Service boundaries
 ╱              ╲  - Firebase emulator tests
╱────────────────╲
        Unit (Vitest)
        - Business logic
        - Utility functions
        - Component rendering
```

### Coverage Goals

| Layer       | Tool               | Target Coverage        |
| ----------- | ------------------ | ---------------------- |
| Unit        | Vitest             | 80%                    |
| Integration | Vitest + Emulators | Key service flows      |
| E2E         | Playwright BDD     | Critical user journeys |

### Part 11 Compliance Testing

E2E tests specifically verify FDA 21 CFR Part 11 requirements:

- **Audit Trail**: Every mutation creates immutable audit log entry
- **Electronic Signatures**: Password re-authentication, signature meaning capture
- **Record Integrity**: Document and release immutability after approval

## Quick Start

```bash
# Run unit tests
pnpm test

# Run with coverage
pnpm test:coverage

# Run E2E tests (requires emulators)
pnpm emulators &  # Terminal 1
pnpm e2e          # Terminal 2
```
