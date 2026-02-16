# ADR-008: Backend-Only Data Access

## Status

Accepted

## Date

2026-01-13

## Context

PactoSigna requires Part 11 compliant audit trails for all data mutations. This necessitates a consistent, server-controlled approach to data access where:

1. All writes must be auditable with server-generated timestamps
2. Business rule validation must occur before persistence
3. Authorization must be enforced consistently
4. Audit log entries must be atomic with the data changes

Additionally, reads must be controlled to enforce:

1. Organization-level data isolation (tenancy)
2. Role-based access control
3. Consistent data transformation and formatting

## Decision

**All Firestore access (both reads and writes) will occur exclusively through the backend REST API.** The frontend will not have direct Firestore access.

### Architecture

```
┌─────────────┐     REST API      ┌─────────────────┐     Firestore SDK     ┌───────────┐
│   Frontend  │ ──────────────▶  │  Fastify API    │ ──────────────────▶  │ Firestore │
│  (React)    │   /api/v2/*      │  (Cloud Run)    │   Admin SDK           │           │
└─────────────┘                  └─────────────────┘                       └───────────┘
      │                                   │
      │                                   ├── Auth validation
      │                                   ├── Organization scoping
      │                                   ├── Business rule enforcement
      │                                   ├── Audit log creation
      │                                   └── Response transformation
      │
      └── Firebase Auth only (for identity tokens)
```

### Implementation

1. **Frontend**: Uses `apiFetch()` wrapper that:
   - Attaches Firebase Auth ID token to requests
   - Handles error responses consistently
   - Provides typed request/response via `@pactosigna/contracts`

2. **Backend**: Fastify REST API that:
   - Validates auth tokens via Firebase Admin SDK
   - Enforces organization membership and role-based access
   - Executes business logic and validation
   - Writes audit log entries atomically with data changes
   - Returns transformed responses matching contract schemas

3. **Data Layer**: `@pactosigna/data` package provides:
   - Repository classes for each aggregate
   - Firestore Admin SDK access
   - Type-safe model definitions

## Consequences

### Positive

- **Consistent audit trails**: All mutations are logged with server timestamps
- **Centralized authorization**: Access control enforced in one place
- **Data isolation**: Organization scoping cannot be bypassed
- **Type safety**: Shared contracts ensure frontend/backend alignment
- **Testability**: Business logic can be unit tested without Firestore emulator

### Negative

- **Latency**: Every operation requires a network round-trip
- **Complexity**: More code than direct Firestore access
- **Offline support**: No offline-first capability (acceptable for QMS use case)

### Neutral

- **Security rules**: Firestore security rules can be simplified to deny all client access
- **Real-time updates**: Not supported (polling or manual refresh required)

## Related Decisions

- [ADR-004: Email/Password Authentication](adr-004-email-password-authentication.md) - Authentication strategy
- [ADR-011: Bounded Context REST API Architecture](adr-011-rest-api-migration.md) - API structure

## Firestore Collection Group Index Caveat

Firebase emulators do **not** enforce index requirements. Collection group queries that work perfectly in the emulator may fail in production with a `FAILED_PRECONDITION` error if the required composite index does not exist in `firestore.indexes.json`.

This means index-related bugs only surface in production or when deploying indexes explicitly. To mitigate:

1. **Document index requirements in tests** - See `DocumentsRepository.test.ts` for examples of tests that document which query shapes require collection group indexes.
2. **Deploy indexes before deploying code** - Run `firebase deploy --only firestore:indexes` before deploying functions that use new query patterns.
3. **Review `firestore.indexes.json`** - When adding new collection group queries (especially with multi-field filters or ordering), verify the corresponding index exists.

Key collection group queries that require indexes:

- `documents` collection group: queries by `organizationId` + `docType` + `status` + `lastSyncedAt` ordering
- `documents` collection group: queries by `organizationId` + `documentId` (for `findByDocumentId`)

## Code References

- Frontend API client: `apps/web/src/shared/services/api/`
- Backend API: `apps/services/src/`
- Data layer: `packages/data/src/`
- Shared contracts: `packages/contracts/src/`
