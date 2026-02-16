# E2E Testing Architecture

## Overview

PactoSigna uses **Behavior-Driven Development (BDD)** with **Playwright** for end-to-end testing. Tests are organized as **user journeys** that map to real-world workflows, enabling parallel execution with worker-isolated test data.

**Architecture Decision**: [ADR-012](../architecture/adr-012-playwright-bdd-testing.md) (original BDD decision), [ADR-017](../architecture/adr-017-journey-based-e2e-testing.md) (journey-based approach)

---

## Key Concepts

### Journey-Based Organization

Tests are organized by user journeys rather than bounded contexts:

| Journey                  | Description                              | Tag                    |
| ------------------------ | ---------------------------------------- | ---------------------- |
| 01-user-onboarding       | Signup, email verification, org creation | `@journey` `@smoke`    |
| 02-repository-connection | GitHub integration, repo selection       | `@journey`             |
| 03-release-workflow      | Create release, sign, approve            | `@journey` `@critical` |
| 04-team-collaboration    | Invite members, manage team              | `@journey`             |
| 05-document-traceability | View links, coverage matrix              | `@journey`             |

### Worker Isolation

Tests run in parallel with isolated data:

- Each Playwright worker gets a unique ID (`w0`, `w1`, etc.)
- All test data is prefixed with the worker ID
- Emulator data is cleared per-worker, not globally
- No test interference even at maximum parallelism

```typescript
// Worker-isolated email transformation
const email = workerContext.transformEmail('user@example.com');
// Result: 'w0-user@example.com' (for worker 0)

// Worker-isolated organization name
const orgName = workerContext.transformOrgName('Acme Medical');
// Result: 'W0 Acme Medical'
```

### Per-Test State Isolation

Step definitions share state through fixtures, not module-level variables:

```typescript
// CORRECT: State from fixture (isolated per-test)
Given('a verified user exists', async ({ journeyContext }) => {
  const { testState } = journeyContext;
  testState.email = result.email;
});

// WRONG: Module-level state (shared across tests in same worker)
const testState = {}; // DON'T DO THIS
```

---

## Directory Structure

```
e2e/
├── playwright.config.ts           # Playwright + BDD configuration
├── globalSetup.ts                 # Emulator health checks
├── globalTeardown.ts              # Cleanup after all tests
├── .features-gen/                 # Auto-generated test files (gitignored)
│
├── features/
│   └── journeys/                  # Journey-based feature files
│       ├── 01-user-onboarding.feature
│       ├── 02-repository-connection.feature
│       ├── 03-release-workflow.feature
│       ├── 04-team-collaboration.feature
│       └── 05-document-traceability.feature
│
├── steps/
│   └── journeys/
│       └── journey.steps.ts       # Consolidated step definitions
│
├── pages/                         # Page Object Models
│   ├── BasePage.ts
│   ├── LoginPage.ts
│   ├── SignupPage.ts
│   ├── DashboardPage.ts
│   ├── OrganizationCreationPage.ts
│   ├── RepositoriesPage.ts
│   ├── DocumentsPage.ts
│   ├── ReleasesPage.ts
│   ├── CreateReleasePage.ts
│   ├── ReleaseDetailPage.ts
│   ├── TraceabilityPage.ts
│   └── TeamManagementPage.ts
│
├── fixtures/                      # Test fixtures
│   ├── bdd-fixture.ts             # Base BDD fixture with worker context
│   ├── journey-fixture.ts         # Journey-specific fixture with seeding
│   ├── test-data.ts               # Centralized test data constants
│   └── emulator/
│       ├── seed.ts                # Emulator seeding functions
│       └── clear-worker.ts        # Worker-specific cleanup
│
└── support/
    ├── hooks.ts                   # Before/After hooks
    ├── github-mocks.ts            # GitHub API/OAuth mocks
    └── world.ts                   # Custom World for sharing state
```

---

## Technology Stack

| Component    | Technology              | Purpose                        |
| ------------ | ----------------------- | ------------------------------ |
| Test Runner  | Playwright Test         | Browser automation             |
| BDD Layer    | playwright-bdd          | Gherkin to Playwright          |
| Assertion    | Playwright expect       | Assertions with auto-waiting   |
| Backend      | Firebase Emulator Suite | Isolated test environment      |
| Data Seeding | Admin SDK               | Deterministic state per worker |

---

## Configuration

### playwright.config.ts

```typescript
import { defineConfig, devices } from '@playwright/test';
import { defineBddConfig } from 'playwright-bdd';

const testDir = defineBddConfig({
  features: 'e2e/features/journeys/**/*.feature',
  steps: 'e2e/steps/journeys/**/*.steps.ts',
  importTestFrom: 'e2e/fixtures/journey-fixture.ts',
});

export default defineConfig({
  testDir,
  fullyParallel: true, // Enable parallel execution
  workers: process.env.CI ? 4 : 2, // More workers in CI
  retries: process.env.CI ? 1 : 0,
  timeout: 60000, // 60s per test

  use: {
    baseURL: 'http://localhost:5003', // Firebase Hosting emulator
    trace: 'on-first-retry',
    screenshot: 'only-on-failure',
  },

  projects: [
    { name: 'smoke', grep: /@smoke/, timeout: 60000 },
    { name: 'critical', grep: /@critical/, timeout: 120000 },
    { name: 'journeys', grep: /@journey/, timeout: 120000 },
  ],
});
```

### Firebase Emulator Integration

Tests run against Firebase Emulators (configured in `firebase.json`):

| Emulator      | Port | Purpose             |
| ------------- | ---- | ------------------- |
| Auth          | 9098 | User authentication |
| Firestore     | 8081 | Database            |
| Functions     | 5002 | API endpoints       |
| Hosting (qms) | 5003 | Web app serving     |
| Emulator UI   | 4001 | Debug interface     |

---

## Feature File Conventions

### File Naming

```
{nn}-{journey-name}.feature
```

Example: `01-user-onboarding.feature`

### Feature Structure

```gherkin
@journey @smoke
Feature: User Onboarding Journey
  As a new user
  I want to sign up, verify my email, and create an organization
  So that I can start using PactoSigna

  @happy-path
  Scenario: New user signs up and creates organization
    Given I am on the signup page
    When I sign up with a new email and password
    And I verify my email via the emulator
    And I log in with my credentials
    Then I should be redirected to organization creation
    When I create an organization with name "Acme Medical"
    Then I should see the dashboard
    And I should see a prompt to connect GitHub
```

### Tag Conventions

| Tag           | Purpose             | When to Use                  |
| ------------- | ------------------- | ---------------------------- |
| `@journey`    | All journey tests   | Always on journey features   |
| `@smoke`      | Quick validation    | Essential user flows         |
| `@critical`   | Core business flows | Release, signature workflows |
| `@happy-path` | Success scenarios   | Primary success paths        |
| `@part11`     | Compliance critical | Audit, signature scenarios   |

---

## Fixtures

### Worker Context (bdd-fixture.ts)

Provides worker-isolated data transformations:

```typescript
interface WorkerTestContext {
  workerId: string; // e.g., "w0"
  transformEmail: (email: string) => string; // Add worker prefix
  transformOrgName: (name: string) => string; // Add worker prefix
  prefix: (name: string) => string; // Generic prefix
}
```

### Journey Context (journey-fixture.ts)

Extends worker context with seeding helpers and per-test state:

```typescript
interface JourneyContext extends WorkerTestContext {
  testState: TestState; // Per-test mutable state
  seedEmptyState: () => Promise<void>; // Clear worker data
  seedUserWithOrg: () => Promise<UserWithOrg>; // Seed verified user
  seedConnectedRepo: () => Promise<RepoData>; // Seed with GitHub
  seedSyncedDocs: () => Promise<DocsData>; // Seed with documents
  seedLinkedDocs: () => Promise<LinksData>; // Seed with links
  mockGitHub: (page: Page) => Promise<void>; // Enable API mocks
  mockGitHubOAuth: (page: Page) => Promise<void>; // Enable OAuth mocks
}
```

---

## Step Definitions

### Pattern

Steps access fixtures through destructuring:

```typescript
import { createBdd } from 'playwright-bdd';
import { test, expect } from '../../fixtures/journey-fixture';

const { Given, When, Then } = createBdd(test);

Given('a verified user with an organization exists', async ({ journeyContext }) => {
  const { testState } = journeyContext;
  const result = await journeyContext.seedUserWithOrg();
  testState.email = result.email;
  testState.password = result.password;
  testState.orgId = result.orgId;
});

When('I log in with my credentials', async ({ page, journeyContext }) => {
  const { testState } = journeyContext;
  const loginPage = new LoginPage(page);
  await loginPage.goto();
  await loginPage.login(testState.email!, testState.password!);
});
```

### Ubiquitous Language

Steps use domain terms from [docs/domain/ubiquitous-language.md](../domain/ubiquitous-language.md):

| Correct      | Incorrect          |
| ------------ | ------------------ |
| Organization | Company, Tenant    |
| Member       | User, Employee     |
| Release      | Version, Package   |
| Signature    | Sign-off, Approval |

---

## Page Objects

### Base Page

```typescript
export abstract class BasePage {
  readonly page: Page;

  constructor(page: Page) {
    this.page = page;
  }

  abstract goto(): Promise<void>;

  async waitForPageLoad(): Promise<void> {
    await this.page.waitForLoadState('networkidle');
  }
}
```

### Example: LoginPage

```typescript
export class LoginPage extends BasePage {
  readonly emailInput: Locator;
  readonly passwordInput: Locator;
  readonly signInButton: Locator;

  constructor(page: Page) {
    super(page);
    this.emailInput = page.getByLabel('Email');
    this.passwordInput = page.getByLabel('Password');
    this.signInButton = page.getByRole('button', { name: 'Sign In' });
  }

  async goto(): Promise<void> {
    await this.page.goto('/login');
    await this.waitForPageLoad();
  }

  async login(email: string, password: string): Promise<void> {
    await this.emailInput.fill(email);
    await this.passwordInput.fill(password);
    await this.signInButton.click();
  }
}
```

---

## Running Tests

### Local Development

```bash
# Run all journey tests
pnpm e2e

# Run smoke tests only (fastest)
pnpm e2e --grep "@smoke"

# Run critical paths
pnpm e2e --grep "@critical"

# Run with UI mode for debugging
pnpm e2e:ui

# Run with visible browser
pnpm e2e:headed

# View HTML report
pnpm e2e:report
```

### CI Pipeline

Tests run in GitHub Actions with 4 parallel workers:

```yaml
jobs:
  e2e:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v4
      - uses: actions/setup-node@v4
      - run: pnpm install
      - run: pnpm exec playwright install --with-deps
      - run: pnpm e2e
      - uses: actions/upload-artifact@v4
        if: failure()
        with:
          name: playwright-report
          path: playwright-report/
```

---

## Test Data Management

### Strategy: Worker-Isolated Emulator Data

Each worker has isolated test data:

1. **Worker prefix**: All data uses `w{n}-` prefix
2. **Cleanup**: `clearWorkerData(workerId)` removes only that worker's data
3. **No global clear**: Tests never clear all emulator data

### Test Data Location

Centralized in `e2e/fixtures/test-data.ts`:

```typescript
export const DEFAULT_MEMBER = {
  email: 'test.member@pactosigna.local',
  password: 'TestMember123!',
  displayName: 'Test Member',
  department: 'Engineering',
};

export const DEFAULT_ORGANIZATION = {
  name: 'Test Organization',
  frameworks: ['ISO_13485', 'IEC_62304'],
};
```

---

## Troubleshooting

### Common Issues

| Issue                       | Solution                           |
| --------------------------- | ---------------------------------- |
| Emulator connection refused | Ensure `pnpm emulators` is running |
| Tests timeout               | Increase timeout, check network    |
| Flaky authentication        | Add explicit waits after login     |
| Data collision              | Verify worker prefixes are applied |

### Debug Mode

```bash
# Playwright Inspector
PWDEBUG=1 pnpm e2e

# Headed browser
pnpm e2e --headed

# Generate trace
pnpm e2e --trace on --grep "User Onboarding"
```

---

## Related Documentation

- [ADR-012: Playwright BDD Testing](../architecture/adr-012-playwright-bdd-testing.md)
- [ADR-017: Journey-Based E2E Testing](../architecture/adr-017-journey-based-e2e-testing.md)
- [Ubiquitous Language](../domain/ubiquitous-language.md)
- [Bounded Contexts](../domain/bounded-contexts.md)
