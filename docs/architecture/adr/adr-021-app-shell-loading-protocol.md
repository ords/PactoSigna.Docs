# ADR-021: App Shell Loading Protocol

## Status

Accepted

## Date

2026-02-06

## Context

Multiple bugs were caused by context race conditions where child components rendered before parent contexts had resolved their data:

1. **OrganizationContext fetching before auth resolved**: On page refresh, Firebase restores the auth session asynchronously from IndexedDB. If OrganizationContext attempted to fetch organizations before auth restored the token, the API returned 401 errors, causing a flash redirect to the login page.
2. **Feature routes rendering before organization loaded**: Components that read `currentOrganization` would crash or show empty states because the organization data had not yet been fetched.
3. **Repository-scoped pages rendering without repository**: Pages that depend on a selected repository would attempt API calls with null organization or repository IDs.

These bugs were intermittent and environment-dependent (faster machines masked the race conditions), making them difficult to reproduce and diagnose.

## Decision

**Parent providers must fully resolve before child components mount.** The App Shell enforces a strict loading cascade:

```
AuthContext (loading) -> OrganizationContext (loading) -> Feature Routes
```

Each level in the cascade:

1. Shows a loading indicator while its data is being resolved.
2. Does not render its children until loading is complete.
3. Provides a clear contract for what data is available when not loading.

### Loading Cascade

| Level | Context             | Waits For               | Resolves When                                                |
| ----- | ------------------- | ----------------------- | ------------------------------------------------------------ |
| 1     | AuthContext         | Nothing                 | First `onAuthStateChanged` callback fires                    |
| 2     | OrganizationContext | `authLoading === false` | Organizations fetched (or skipped for unauthenticated users) |
| 3     | Feature Routes      | `orgLoading === false`  | Render with `currentOrganization` guaranteed available       |

### Implementation Rules

1. **AuthContext** initializes `loading: true` and sets it to `false` after the first `onAuthStateChanged` callback. This happens exactly once per app lifecycle.

2. **OrganizationContext** checks `authLoading` in its fetch effect. If `authLoading` is `true`, the effect returns early without doing anything. Its own `loading` starts as `true` and stays that way until auth resolves AND organizations are fetched.

3. **Feature routes** (inside `ProtectedRoute` or equivalent) must not render until `loading` from OrganizationContext is `false`. They can rely on `currentOrganization` being non-null for authenticated users with at least one organization.

4. **RepositoryContext** is demand-driven (not part of the cascade). It starts idle and only loads data when `selectRepository` is called explicitly. Pages that need repository data call `selectRepository` in their ViewModel hooks.

### Loading Indicator Placement

- The App Shell renders a full-page loading spinner while AuthContext or OrganizationContext is loading.
- Feature pages handle their own loading states (e.g., skeleton screens) for page-specific data fetched via ViewModel hooks (see ADR-020).

## Consequences

### Positive

- **No race conditions**: Child components never render before their required context data is available. The sequential cascade eliminates timing-dependent bugs.
- **Predictable loading behavior**: Each context has a documented contract for when `loading` transitions and what data is available afterward.
- **Simpler child components**: Feature pages do not need defensive null checks for auth or organization data -- the cascade guarantees availability.

### Negative

- **Slightly longer perceived load time**: The sequential cascade means organization data cannot be fetched in parallel with auth resolution. In practice, auth resolution from IndexedDB is fast (< 100ms), so the added latency is negligible.
- **Rigid ordering**: Adding a new context layer to the cascade requires updating the loading protocol documentation and potentially the App Shell component tree.

### Neutral

- **Consistent pattern**: All contexts follow the same loading contract, making it easy for developers to understand and extend.
- **Testing**: Integration tests must account for the cascade by providing resolved auth state before testing organization-dependent components.

## Related Decisions

- [ADR-008: Backend-Only Data Access](adr-008-backend-only-firestore-writes.md) - Organization data fetched via REST API
- [ADR-020: ViewModel Hook Pattern](adr-020-viewmodel-hook-pattern.md) - Page-level data fetching that depends on resolved contexts
