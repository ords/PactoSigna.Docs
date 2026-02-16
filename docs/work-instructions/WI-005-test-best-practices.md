# Testing Best Practices

This guide documents testing patterns and anti-patterns for PactoSigna. Following these practices ensures tests are reliable, fast, and maintainable.

## Related Documentation

- [E2E Architecture](./e2e-architecture.md) - Overall E2E testing structure
- [ADR-012: Playwright BDD Testing](../architecture/adr-012-playwright-bdd-testing.md) - Why we chose Playwright + BDD
- [ADR-016: External Service Abstraction](../architecture/adr-016-external-service-abstraction.md) - How external services are mocked

---

## Anti-Pattern: Hard-Coded Timeouts

### Problem

```typescript
// ❌ BAD: Arbitrary delays cause flaky tests
await page.click('#submit');
await page.waitForTimeout(2000); // Why 2 seconds? Will it always be enough?
await expect(page.locator('.result')).toBeVisible();
```

Hard-coded timeouts are problematic because:

- They're arbitrary guesses that may be too short (flaky) or too long (slow)
- They don't adapt to actual application state
- They hide the real condition being waited for

### Solution: Condition-Based Waiting

```typescript
// ✅ GOOD: Wait for the actual condition
await page.click('#submit');
await expect(page.locator('.result')).toBeVisible(); // Auto-retries until visible
```

Playwright's web-first assertions automatically retry until the condition is met or timeout is reached.

### Common Patterns

**Waiting for element state:**

```typescript
// Wait for element to be visible
await expect(page.getByRole('button', { name: 'Submit' })).toBeVisible();

// Wait for element to be enabled
await expect(page.getByRole('button', { name: 'Submit' })).toBeEnabled();

// Wait for element to be hidden/removed
await expect(page.getByRole('dialog')).toBeHidden();
```

**Waiting for navigation:**

```typescript
// Wait for URL change
await expect(page).toHaveURL('/dashboard');

// Wait for specific content after navigation
await page.getByRole('link', { name: 'Dashboard' }).click();
await expect(page.getByRole('heading', { name: 'Dashboard' })).toBeVisible();
```

**Waiting for network/loading states:**

```typescript
// Wait for loading indicator to disappear
await expect(page.getByTestId('loading-spinner')).toBeHidden();

// Wait for table to have data
await expect(page.getByRole('row')).toHaveCount(5);
```

**Waiting for dropdown/modal to close:**

```typescript
// After selecting from dropdown, wait for listbox to close
await page.getByRole('combobox').click();
await page.getByRole('option', { name: 'Option 1' }).click();
await page.getByRole('listbox').waitFor({ state: 'hidden' });
```

### When Timeouts Are Acceptable

The only acceptable use of `waitForTimeout` is for **debugging**:

```typescript
// Temporarily pause to inspect state (remove before committing)
await page.waitForTimeout(5000); // DEBUG ONLY
```

---

## Anti-Pattern: MUI Animation Timing in Unit Tests

### Problem

```typescript
// ❌ BAD: Test fails intermittently due to MUI animation timing
it('closes modal when clicking close button', async () => {
  render(<MyModal open />);
  await user.click(screen.getByRole('button', { name: 'Close' }));
  // This may fail because MUI's fade animation hasn't completed
  expect(screen.queryByRole('dialog')).not.toBeInTheDocument();
});
```

MUI components use CSS transitions (typically 225ms). In tests, this causes:

- Race conditions between assertion and animation completion
- Flaky tests that pass locally but fail in CI
- Unnecessary test slowdown

### Solution: Disable Transitions in Test Theme

Configure the test theme to disable all MUI transitions:

```typescript
// apps/web/src/test/test-utils.tsx
import { createTheme, ThemeProvider } from '@mui/material/styles';

const testTheme = createTheme({
  transitions: {
    // Disable all transitions
    create: () => 'none',
    duration: {
      shortest: 0,
      shorter: 0,
      short: 0,
      standard: 0,
      complex: 0,
      enteringScreen: 0,
      leavingScreen: 0,
    },
  },
});

export function renderWithProviders(ui: React.ReactElement) {
  return render(
    <ThemeProvider theme={testTheme}>
      {ui}
    </ThemeProvider>
  );
}
```

Then use `renderWithProviders` instead of `render`:

```typescript
// ✅ GOOD: Transitions disabled, test is synchronous
it('closes modal when clicking close button', async () => {
  renderWithProviders(<MyModal open />);
  await user.click(screen.getByRole('button', { name: 'Close' }));
  expect(screen.queryByRole('dialog')).not.toBeInTheDocument(); // Immediate
});
```

---

## External Services in Tests

### Email Testing

Emails are handled by `NoOpEmailService` in emulator environments (see [ADR-016](../architecture/adr-016-external-service-abstraction.md)).

**E2E tests don't need to verify email delivery** - they test the application flow:

```typescript
// Test the invite flow without actual email
await adminPage.inviteMember('new@example.com', 'Engineering');
// Verify invite appears in UI
await expect(page.getByText('new@example.com')).toBeVisible();
// Verify invite status
await expect(page.getByText('Pending')).toBeVisible();
```

**For email verification flows**, use Firebase Auth Emulator's OOB codes API:

```typescript
// e2e/fixtures/emulator/seed.ts
export async function verifyEmailViaEmulator(email: string): Promise<void> {
  // Fetch OOB codes from emulator
  const response = await fetch(`http://127.0.0.1:9098/emulator/v1/projects/${projectId}/oobCodes`);
  const { oobCodes } = await response.json();

  // Find verification code for this email
  const code = oobCodes.find((c: any) => c.email === email && c.requestType === 'VERIFY_EMAIL');

  // Trigger verification via emulator
  await fetch(`http://127.0.0.1:9098/emulator/action?mode=verifyEmail&oobCode=${code.oobCode}`);
}
```

### GitHub API Testing

In unit tests, mock the GitHub service:

```typescript
vi.mock('@pactosigna/data', async importOriginal => {
  const actual = await importOriginal();
  return {
    ...actual,
    createGitHubServiceFromEnv: () => ({
      getRepositories: vi
        .fn()
        .mockResolvedValue([{ id: 123, name: 'test-repo', full_name: 'org/test-repo' }]),
    }),
  };
});
```

---

## Test Organization

### Use Descriptive Test Names

```typescript
// ❌ BAD: Unclear what's being tested
it('works', async () => {
  /* ... */
});
it('handles error', async () => {
  /* ... */
});

// ✅ GOOD: Describes behavior and context
it('should display validation error when email is invalid', async () => {
  /* ... */
});
it('should redirect to login when session expires', async () => {
  /* ... */
});
```

### Group Related Tests

```typescript
describe('OrganizationContext', () => {
  describe('when user is authenticated', () => {
    it('should fetch organizations', async () => {
      /* ... */
    });
    it('should auto-select first organization', async () => {
      /* ... */
    });
  });

  describe('when user is not authenticated', () => {
    it('should clear organization state', async () => {
      /* ... */
    });
    it('should not call API', async () => {
      /* ... */
    });
  });

  describe('error handling', () => {
    it('should show error toast on network failure', async () => {
      /* ... */
    });
    it('should silently handle 401 errors', async () => {
      /* ... */
    });
  });
});
```

### Test What Matters

Focus on behavior that prevents real risks:

```typescript
// ✅ GOOD: Tests meaningful business logic
it('should NOT fetch invites for non-admin users', async () => {
  // This prevents security/authorization bugs
});

it('should filter to only pending invites', async () => {
  // This prevents showing incorrect data
});

// ❌ BAD: Tests implementation details
it('should call useState with initial value', async () => {
  // This doesn't prevent any real bugs
});
```

---

## Firestore Composite Index Testing

The Firestore emulator does **not** enforce composite index requirements. Missing indexes only fail in production with a 500 error. To catch these gaps before deployment, we maintain index regression tests in `packages/data/src/repositories/firestore-indexes.test.ts`.

### When to Update

Any time you add or modify a Firestore query in `packages/data/src/repositories/` that requires a composite index, you must:

1. Add the index to `firebase/firestore.indexes.json`
2. Add a `hasIndex(...)` assertion in `firestore-indexes.test.ts`

A query requires a composite index when it uses:

- `where()` + `orderBy()` on a different field
- Multiple `where()` clauses where any uses `in`, `array-contains`, `!=`, or range operators

### Example

```typescript
// In firestore-indexes.test.ts
it('should have index for findLastPublished WITH deviceId', () => {
  expect(
    hasIndex('releases', 'COLLECTION', [
      { fieldPath: 'status', order: 'ASCENDING' },
      { fieldPath: 'type', order: 'ASCENDING' },
      { fieldPath: 'deviceId', order: 'ASCENDING' },
      { fieldPath: 'publishedAt', order: 'DESCENDING' },
    ])
  ).toBe(true);
});
```

The field order and directions must match what Firestore requires for the query. Name the test after the repository method and describe the query shape.

---

## Firebase Emulator Usage

### Required Ports

| Service     | Port |
| ----------- | ---- |
| Auth        | 9098 |
| Firestore   | 8081 |
| Functions   | 5002 |
| Emulator UI | 4001 |

### Seeding Test Data

Use the seed helpers in `e2e/fixtures/emulator/seed.ts`:

```typescript
// Create a verified user
const user = await seedVerifiedMember({
  email: 'test@example.com',
  password: 'TestPass123!',
  displayName: 'Test User',
});

// Create organization with member
const { organization, member } = await seedOrganizationWithMember(user.uid, {
  name: 'Test Org',
  role: 'admin',
  department: 'Engineering',
});
```

### Cleaning Up Between Tests

Each test should start with a clean state:

```typescript
beforeEach(async () => {
  await clearEmulatorData();
});
```

---

## Coverage Guidelines

Minimum thresholds (configured in `vitest.config.ts`):

| Metric     | Threshold |
| ---------- | --------- |
| Statements | 80%       |
| Branches   | 76%       |
| Functions  | 80%       |
| Lines      | 80%       |

Focus coverage on:

- Error handling paths
- Authorization checks
- Business logic branches
- Edge cases (empty arrays, null values, etc.)

Don't chase coverage for:

- Simple getters/setters
- Framework boilerplate
- Type definitions
