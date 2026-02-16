# ADR-017: Journey-Based E2E Testing with Worker Isolation

## Status

Accepted

## Date

2026-02-03

## Context

The existing E2E test architecture (ADR-012) organized tests by bounded context (identity-access, repository-connection, etc.). While this aligned with domain-driven design, it created several problems:

1. **Sequential execution required** - Tests shared emulator state, requiring `workers: 1` and `fullyParallel: false`
2. **Slow CI feedback** - Tests ran sequentially, extending CI time significantly
3. **Flaky tests** - Shared state led to order-dependent failures
4. **Complex step reuse** - Steps were duplicated across context-specific files
5. **Artificial boundaries** - Real user flows span multiple contexts

### Problem Statement

The original architecture enforced test isolation through global emulator clearing between tests. This meant:

- Each test cleared ALL emulator data before running
- Tests could not run in parallel (data collision)
- CI took longer as test suite grew
- Retries were expensive (required full re-seed)

### Requirements

1. Enable parallel test execution (multiple workers)
2. Eliminate flaky tests from shared state
3. Reduce CI execution time by 75%+
4. Maintain BDD/Gherkin approach from ADR-012
5. Keep tests readable and maintainable

## Decision

We will reorganize E2E tests around **user journeys** with **worker-isolated test data**.

### Key Changes

| Aspect         | Before (ADR-012)       | After (ADR-017)      |
| -------------- | ---------------------- | -------------------- |
| Organization   | By bounded context     | By user journey      |
| Parallelism    | Sequential (1 worker)  | Parallel (4 workers) |
| Data isolation | Global clear           | Worker-prefixed data |
| Step files     | Per-context            | Consolidated         |
| State sharing  | Module-level variables | Fixture-scoped       |

### Worker Isolation Strategy

Each Playwright worker receives a unique identifier (w0, w1, w2, w3). All test data is prefixed:

```
Worker 0: w0-user@example.com, W0 Test Org
Worker 1: w1-user@example.com, W1 Test Org
Worker 2: w2-user@example.com, W2 Test Org
Worker 3: w3-user@example.com, W3 Test Org
```

Workers never clear each other's data. Cleanup is scoped: `clearWorkerData(workerId)` only removes data with that worker's prefix.

### Journey Organization

Tests are organized by end-to-end user workflows:

1. **User Onboarding** - Signup → Email verification → Org creation
2. **Repository Connection** - GitHub install → Repo selection → Sync
3. **Release Workflow** - Create release → Sign → Approve
4. **Team Collaboration** - Invite members → Accept → View team
5. **Document Traceability** - View links → Coverage matrix → Gap analysis

### Per-Test State Isolation

Step definitions access state through fixtures, not module-level variables:

```typescript
// BEFORE: Module-level state (bug: shared across tests in same worker)
const testState: TestState = {};

// AFTER: Fixture-scoped state (correct: isolated per-test)
Given('a verified user exists', async ({ journeyContext }) => {
  const { testState } = journeyContext;
  // testState is fresh for each test
});
```

### Rationale

1. **Journeys match reality** - Users don't think in bounded contexts; they complete workflows
2. **Isolation enables parallelism** - Worker prefixes prevent data collision
3. **Consolidated steps reduce maintenance** - One file instead of five context-specific files
4. **Fixtures enforce correctness** - Compiler catches missing context destructuring

## Alternatives Considered

### A) Keep Bounded Context Organization, Add Worker Isolation

- **Pros**: Less restructuring, keeps DDD alignment
- **Cons**: Still duplicates steps, artificial test boundaries

### B) Database-per-Worker (Separate Firestore Projects)

- **Pros**: Complete isolation
- **Cons**: Complex setup, resource-intensive, no emulator support

### C) Test Sharding by File

- **Pros**: Simple to implement
- **Cons**: Doesn't fix shared state within files, uneven distribution

## Consequences

### Positive

- **4x faster CI** - Parallel execution with 4 workers
- **Eliminated flakiness** - No shared state between tests
- **Simpler maintenance** - One step file instead of five
- **Natural grouping** - Journeys match user mental model
- **Compiler safety** - Missing state access caught at build time

### Negative

- **Migration effort** - Existing tests required refactoring
- **Learning curve** - Team needs to understand fixture scoping
- **Documentation update** - e2e-architecture.md required rewrite

### Neutral

- **Different organization** - Neither better nor worse for domain alignment
- **Tag changes** - `@journey` replaces `@identity-access` etc.

## Implementation

### Directory Structure

```
e2e/
├── features/journeys/           # Journey feature files
│   ├── 01-user-onboarding.feature
│   ├── 02-repository-connection.feature
│   ├── 03-release-workflow.feature
│   ├── 04-team-collaboration.feature
│   └── 05-document-traceability.feature
├── steps/journeys/
│   └── journey.steps.ts         # Consolidated steps
└── fixtures/
    ├── bdd-fixture.ts           # Worker context
    └── journey-fixture.ts       # Journey context + testState
```

### Configuration

```typescript
// playwright.config.ts
export default defineConfig({
  fullyParallel: true,
  workers: process.env.CI ? 4 : 2,
  // ...
});
```

### Fixture Usage

```typescript
// journey-fixture.ts
export interface JourneyContext extends WorkerTestContext {
  testState: TestState; // Per-test, not per-worker
  seedUserWithOrg: () => Promise<UserWithOrg>;
  // ...
}

export const test = bddTest.extend<{ journeyContext: JourneyContext }>({
  journeyContext: async ({ workerContext }, use) => {
    const testState: TestState = {}; // Fresh per-test
    // ...
  },
});
```

## References

- [ADR-012: Playwright BDD Testing](./adr-012-playwright-bdd-testing.md) - Original BDD decision
- [E2E Architecture](../testing/e2e-architecture.md) - Implementation details
- [Playwright Worker Isolation](https://playwright.dev/docs/test-parallel#worker-processes)
- [playwright-bdd Fixtures](https://vitalets.github.io/playwright-bdd/#/writing-steps/playwright-fixtures)
