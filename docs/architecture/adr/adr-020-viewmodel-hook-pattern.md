# ADR-020: ViewModel Hook Pattern

## Status

Accepted

## Date

2026-02-06

## Context

A sprint retrospective identified that several page components had grown to 600-1100 lines, mixing data fetching logic (React Query calls, derived state calculations, mutation handlers) with rendering. This caused several problems:

1. **Race conditions**: Multiple `useQuery` and `useMutation` hooks interleaved with `useEffect` calls created subtle timing bugs where derived state depended on data that had not yet loaded.
2. **Difficult testing**: Testing a page component required rendering the full component tree with providers, making it hard to test data logic independently from UI rendering.
3. **Poor readability**: Scrolling through hundreds of lines of hooks to find the JSX return made code review slow and error-prone.
4. **Duplicated patterns**: Several pages independently re-implemented the same loading/error/empty-state logic.

## Decision

**All page-level data fetching must use custom ViewModel hooks** that encapsulate React Query calls, derived state, and mutation handlers.

### Structure

Each page component is split into two parts:

1. **ViewModel hook** (e.g., `useReleaseDetail`): Contains all `useQuery`/`useMutation` calls, derived state calculations, and event handlers. Returns a typed object with the page's complete state and actions.
2. **Page component** (e.g., `ReleaseDetailPage`): A pure rendering component that calls the ViewModel hook and maps the returned state to UI components.

### Naming Convention

- Hook: `use<PageName>` (e.g., `useReleaseDetail`, `useDocumentDetail`)
- File: `use<PageName>.ts` in the feature's `hooks/` directory
- Page: `<PageName>Page.tsx` in the feature's `pages/` directory

### Example

```typescript
// hooks/useReleaseDetail.ts
export function useReleaseDetail(releaseId: string) {
  const { currentOrganization } = useOrganization();
  const orgId = currentOrganization!.organization.id;

  const releaseQuery = useQuery({
    queryKey: ['release', orgId, releaseId],
    queryFn: () => getReleaseDetails({ organizationId: orgId, releaseId }),
  });

  const approveMutation = useMutation({
    mutationFn: (signatureId: string) =>
      approveRelease({ organizationId: orgId, releaseId, signatureId }),
    onSuccess: () => releaseQuery.refetch(),
  });

  return {
    release: releaseQuery.data?.release ?? null,
    isLoading: releaseQuery.isLoading,
    error: releaseQuery.error,
    approve: approveMutation.mutate,
    isApproving: approveMutation.isPending,
  };
}

// pages/ReleaseDetailPage.tsx
export function ReleaseDetailPage() {
  const { releaseId } = useParams();
  const vm = useReleaseDetail(releaseId!);

  if (vm.isLoading) return <LoadingSkeleton />;
  if (vm.error) return <ErrorDisplay error={vm.error} />;
  if (!vm.release) return <NotFound />;

  return <ReleaseDetailView release={vm.release} onApprove={vm.approve} />;
}
```

### Guidelines

- ViewModel hooks must not return JSX or React elements.
- Page components should be under 200 lines. If a page exceeds this, extract sub-components.
- ViewModel hooks may compose other hooks (e.g., `useOrganization`, `useRepository`).
- React Query handles caching, deduplication, and background refetching -- ViewModel hooks should not re-implement these behaviors.

## Consequences

### Positive

- **Testable data logic**: ViewModel hooks can be tested with `renderHook` without rendering the full page UI.
- **Small page components**: Pages become pure rendering functions, typically under 200 lines.
- **Eliminated race conditions**: React Query manages loading/error/stale states consistently; derived state is computed from query results rather than synchronized via effects.
- **Reusable data logic**: ViewModel hooks can be shared across pages that need the same data.

### Negative

- **One more file per page**: Each page now has a corresponding hook file, increasing the file count in feature directories.
- **Indirection**: Developers must look at the hook file to understand what data a page uses.

### Neutral

- **Gradual adoption**: Existing pages can be refactored incrementally. New pages must follow this pattern from the start.

## Related Decisions

- [ADR-008: Backend-Only Data Access](adr-008-backend-only-firestore-writes.md) - All data fetching goes through REST APIs
- [ADR-021: App Shell Loading Protocol](adr-021-app-shell-loading-protocol.md) - Context loading cascade that ViewModel hooks depend on
