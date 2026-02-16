---
id: SDD-005
title: App Shell Provider Chain
status: Draft
links:
  source:
    - docs/architecture/SAD-003-module-boundaries.md
  related:
    - docs/design/SDD-006-viewmodel-hooks-data-fetching.md
---

# SDD-005 App Shell Provider Chain

## Purpose

Document provider layering and loading dependency rules for frontend app shell stability.

## Provider Order

- Query and theme providers wrap global UI state.
- Auth provider resolves identity first.
- Organization provider waits for auth completion.
- Repository provider supplies repository/device/QMS selection context.

## Critical Rule

No child provider may resolve loading state before parent resolution is complete.

## Expected Outcome

Deterministic loading and route behavior without premature redirects.


---

## Frontend Architecture Patterns

Full design: See ARCH docs ([HLD-001](../architecture/HLD-001-system-context.md) through [SAD-003](../architecture/SAD-003-module-boundaries.md)) and DD docs ([SDD-005](../design/SDD-005-app-shell-provider-chain.md) through [SDD-008](../design/SDD-008-routing-navigation-guards.md)).

## App Shell Provider Chain

```
AuthProvider → OrganizationProvider → RepositoryProvider → DashboardLayout → Pages
```

**Critical rule**: No context may set `loading=false` until its parent has resolved. OrganizationProvider must wait for `authLoading` to complete before making decisions.

## Data Fetching Pattern (ViewModel Hooks)

Use React Query hooks as ViewModels. Pages should only compose hooks and render.

```typescript
// TARGET PATTERN: Custom hook (ViewModel)
function useDocumentDetail(documentId: string) {
  const { currentOrganization } = useOrganization();
  return useQuery({
    queryKey: ['documents', currentOrganization?.organization.id, documentId],
    queryFn: () => getDocument({ organizationId: ..., documentId }),
    enabled: !!currentOrganization,
  });
}

// Page just composes
function DocumentDetailPage() {
  const { documentId } = useParams();
  const { data, isLoading, error } = useDocumentDetail(documentId!);
  // render only
}
```

**Do not**: Put useState + useEffect data fetching in page components. Do not create `Extended*Response` types — fix the contract instead.

## Feature Module Structure

```
features/{name}/
├── hooks/         ViewModel hooks (data fetching + state)
├── components/    Presentational components
├── pages/         Page-level composition only
├── context/       Only if feature has cross-page state
└── index.ts       Barrel export
```

## Contract Types at Boundaries

- API functions in `shared/services/api/` use types from `@pactosigna/contracts`
- Never define response types in frontend code — update contracts
- Hooks map contract types to view-friendly shapes internally
