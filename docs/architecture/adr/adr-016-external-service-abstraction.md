# ADR-016: External Service Abstraction for Testability

## Status

Accepted

## Date

2026-02-02

## Context

PactoSigna integrates with several external services:

- **Microsoft Graph API** - Email delivery for invitations and notifications
- **GitHub API** - Repository access, webhook handling, file sync
- **Google Cloud Tasks** - Background job processing
- **Firebase Auth** - User authentication
- **Cloud Firestore** - Data persistence

These integrations create challenges for testing:

1. **E2E tests** should not send real emails or make real GitHub API calls
2. **Unit tests** need fast, deterministic execution without network dependencies
3. **Local development** should work without production credentials
4. **CI pipelines** run in isolated environments without external access

We need a consistent pattern for handling external services across environments.

## Decision

**All external services will be abstracted behind interfaces with environment-aware factory functions.**

### Pattern

```typescript
// 1. Define the interface
export interface EmailService {
  sendInviteEmail(params: InviteEmailParams): Promise<void>;
}

// 2. Implement production version
export class GraphEmailService implements EmailService {
  async sendInviteEmail(params: InviteEmailParams): Promise<void> {
    // Real Microsoft Graph API call
  }
}

// 3. Implement test/emulator version
export class NoOpEmailService implements EmailService {
  async sendInviteEmail(params: InviteEmailParams): Promise<void> {
    console.log('[NoOpEmailService] Would send email:', params);
  }
}

// 4. Factory function with environment detection
export function createEmailService(): EmailService {
  // Use truthy check (matches firebase-functions SDK behavior)
  const isEmulator =
    !!process.env.FUNCTIONS_EMULATOR ||
    !!process.env.FIRESTORE_EMULATOR_HOST ||
    !!process.env.FIREBASE_AUTH_EMULATOR_HOST;
  const hasCredentials = process.env.AZURE_CLIENT_ID && process.env.AZURE_CLIENT_SECRET;

  if (isEmulator || !hasCredentials) {
    return new NoOpEmailService();
  }
  return new GraphEmailService();
}
```

### Service-Specific Strategies

| Service               | Production             | Emulator/Local                              | Unit Tests        |
| --------------------- | ---------------------- | ------------------------------------------- | ----------------- |
| **Email (Graph API)** | `GraphEmailService`    | `NoOpEmailService` (logs to console)        | Mock via DI       |
| **GitHub API**        | `OctokitGitHubService` | `OctokitGitHubService` (if token available) | Mock via DI       |
| **Cloud Tasks**       | Queue to Cloud Tasks   | Inline processing                           | Mock via DI       |
| **Firebase Auth**     | Firebase Auth          | Auth Emulator (port 9098)                   | Mock `useAuth`    |
| **Firestore**         | Cloud Firestore        | Firestore Emulator (port 8081)              | Mock repositories |

### Environment Detection

```typescript
// Firebase emulator detection - use truthy check to match firebase-functions SDK behavior
// This handles cases where the value might be "true", "1", "TRUE", etc.
const isEmulator =
  !!process.env.FUNCTIONS_EMULATOR ||
  !!process.env.FIRESTORE_EMULATOR_HOST ||
  !!process.env.FIREBASE_AUTH_EMULATOR_HOST;

// CI detection
const isCI = process.env.CI === 'true';

// Credential availability
const hasAzureCredentials = !!(
  process.env.AZURE_TENANT_ID &&
  process.env.AZURE_CLIENT_ID &&
  process.env.AZURE_CLIENT_SECRET
);
```

### Implementation Guidelines

1. **Always use interfaces** - Services should depend on interfaces, not concrete implementations
2. **Constructor injection** - Pass service instances via constructor for testability
3. **Factory at composition root** - Call factory functions in controllers/entry points, not deep in business logic
4. **Log in NoOp implementations** - Console logging helps debug test flows
5. **Same interface contract** - NoOp implementations must satisfy the full interface

## Consequences

### Positive

- **Testability**: Unit tests run fast without network calls
- **Local development**: Works without production credentials
- **CI reliability**: No flaky tests from external service failures
- **Consistency**: Same pattern across all external integrations
- **Debugging**: Console logs show what would happen in production

### Negative

- **Code overhead**: Interface + multiple implementations per service
- **Behavior differences**: NoOp services don't catch integration bugs
- **Maintenance**: Must keep implementations in sync

### Mitigations

- **Integration tests**: Separate test suite with real services (not run on every PR)
- **Contract tests**: Verify NoOp implementations match interface contracts
- **Staging environment**: Full integration testing before production

## Examples

### Email Service (Implemented)

```typescript
// packages/data/src/services/EmailService.ts
export interface EmailService {
  sendInviteEmail(params: InviteEmailParams): Promise<void>;
}

export class GraphEmailService implements EmailService {
  /* ... */
}
export class NoOpEmailService implements EmailService {
  /* ... */
}
export function createEmailService(): EmailService {
  /* ... */
}

// apps/services/src/modules/identity/IdentityController.ts
const emailService = createEmailService();
const invitesService = new InvitesService(/* ..., */ emailService /* ... */);
```

### GitHub Service (Existing)

```typescript
// packages/data/src/services/GitHubService.ts
export interface GitHubService {
  getRepositories(installationId: number): Promise<GitHubRepository[]>;
  // ...
}

export class OctokitGitHubService implements GitHubService {
  /* ... */
}
export function createGitHubServiceFromEnv(): GitHubService | null {
  /* ... */
}
```

## Related Decisions

- [ADR-008: Backend-Only Data Access](adr-008-backend-only-firestore-writes.md) - Data layer architecture
- [ADR-012: Playwright BDD Testing](adr-012-playwright-bdd-testing.md) - E2E testing strategy

## Code References

- Email service: `packages/data/src/services/EmailService.ts`
- GitHub service: `packages/data/src/services/GitHubService.ts`
- Service exports: `packages/data/src/services/index.ts`
- Firebase emulator config: `firebase.json`
