# ADR-011: Bounded Context REST API Architecture

## Status

Accepted

## Date

2026-01-15

## Context

PactoSigna follows Domain-Driven Design principles with distinct bounded contexts. The REST API needs to:

1. Reflect the bounded context structure in its organization
2. Provide type-safe contracts between frontend and backend
3. Support OpenAPI documentation for external integrations
4. Enable independent evolution of each context's API

## Decision

Organize the REST API around bounded contexts with a shared contracts package for type safety.

### API Structure

```
/api/v2/
├── /organizations         # Identity & Access Context
│   ├── GET    /                     # List user's organizations
│   ├── POST   /                     # Create organization
│   ├── GET    /:orgId               # Get organization details
│   ├── GET    /:orgId/members       # List members
│   ├── POST   /:orgId/members       # Add member
│   ├── GET    /:orgId/invites       # List invites
│   ├── POST   /:orgId/invites       # Create invite
│   └── GET    /:orgId/qms           # Get organization's QMS
│
├── /repositories          # Repository & Owner Management Context
│   ├── GET    /available            # List available GitHub repos
│   ├── GET    /connected            # List connected repos
│   ├── POST   /connect              # Connect a repository
│   └── POST   /:repoId/sync         # Trigger sync
│
├── /qms                   # Repository Owner (QMS)
│   └── GET    /:qmsId               # Get QMS details
│
├── /devices               # Repository Owner (Device)
│   ├── GET    /                     # List devices
│   ├── POST   /                     # Create device
│   └── GET    /:deviceId            # Get device details
│
├── /documents             # Document Management Context
│   ├── GET    /                     # List documents (with filters)
│   ├── GET    /:docId               # Get document details
│   └── GET    /:docId/content       # Get rendered content
│
├── /releases              # Release Management Context
│   ├── GET    /                     # List releases
│   ├── POST   /                     # Create release
│   ├── GET    /:releaseId           # Get release details
│   ├── POST   /:releaseId/submit    # Submit for review
│   ├── POST   /:releaseId/withdraw  # Withdraw from review
│   ├── POST   /:releaseId/publish   # Publish release
│   ├── POST   /:releaseId/signatures # Add signature
│   └── POST   /:releaseId/export    # Request auditor export
│
├── /change-controls       # Change Control (QMS releases)
│   ├── GET    /                     # List change controls
│   ├── POST   /                     # Create change control
│   └── GET    /:changeId            # Get change control details
│
├── /risks                 # Risk Management (within Document Context)
│   └── GET    /matrix               # Get risk matrix
│
├── /signatures            # Signature Management
│   └── GET    /eligibility          # Check signature eligibility
│
└── /audit                 # Audit Context (Shared Kernel)
    └── GET    /logs                 # Query audit logs
```

### Shared Contracts Package

The `@pactosigna/contracts` package provides:

```typescript
// Request/Response schemas with Zod validation
export const CreateReleaseRequestSchema = z.object({
  organizationId: z.string(),
  name: z.string().min(1),
  description: z.string().optional(),
  type: z.enum(['qms', 'device']),
  deviceId: z.string().optional(),
  changes: z.array(ReleaseChangeSchema),
});

export type CreateReleaseRequest = z.infer<typeof CreateReleaseRequestSchema>;

// Response types
export interface ReleaseResponse {
  id: string;
  name: string;
  status: ReleaseStatus;
  // ... other fields
}
```

### Module Organization

Backend modules mirror bounded contexts:

```
apps/services/src/modules/
├── identity/           # Organizations, Members, Invites
├── repositories/       # Repository connection & sync
├── qms/               # QMS entity management
├── devices/           # Device entity management
├── documents/         # Document indexing & retrieval
├── releases/          # Release workflow
├── changeControls/    # QMS change controls
├── risks/             # Risk matrix
├── signatures/        # Signature collection
├── productReleases/   # Product releases (device)
└── audit/             # Audit log queries
```

## Consequences

### Positive

- **Clear boundaries**: API structure reflects domain model
- **Type safety**: Zod schemas validate at runtime, TypeScript at compile time
- **Discoverability**: Developers can navigate API by bounded context
- **Independent evolution**: Each context can evolve its API independently

### Negative

- **Learning curve**: Developers must understand DDD concepts
- **Duplication**: Some patterns repeated across contexts

### Neutral

- **Versioning**: `/api/v2` prefix allows future breaking changes
- **OpenAPI**: Can be generated from Zod schemas for documentation

## Related Decisions

- [ADR-008: Backend-Only Data Access](adr-008-backend-only-firestore-writes.md) - Data access pattern
- [ADR-014: Shared Package Purpose](adr-014-shared-package-purpose.md) - Package organization

## Code References

- Contracts package: `packages/contracts/src/`
- API modules: `apps/services/src/modules/`
- Frontend API clients: `apps/web/src/shared/services/api/`
