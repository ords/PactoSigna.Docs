# Improvement Plan - Post-Retrospective

> **Created**: 2026-02-06
> **Context**: Sprint retrospective identified 18 bugs across 12 PRs in one week, with 6 root cause categories
> **Goal**: Bring the project to a maintainable, well-documented state that prevents recurrence

## What Was Already Done (This Branch)

| Deliverable                                  | Status | Files                                                                   |
| -------------------------------------------- | ------ | ----------------------------------------------------------------------- |
| HLD - Software Architecture                  | Done   | `docs/architecture/hld-software-architecture.md`                        |
| SDD - Frontend Architecture                  | Done   | `docs/architecture/sdd-frontend-architecture.md`                        |
| SDD - Backend Architecture                   | Done   | `docs/architecture/sdd-backend-architecture.md`                         |
| CLAUDE.md updated with frontend patterns     | Done   | `CLAUDE.md`                                                             |
| Code-reviewer agent updated                  | Done   | `.claude/agents/quality/code-reviewer.agent.md`                         |
| React frontend engineer agent updated        | Done   | `.claude/agents/implementation/expert-react-frontend-engineer.agent.md` |
| Training context documented (was "Deferred") | Done   | `docs/domain/bounded-contexts.md`                                       |
| Ubiquitous language expanded                 | Done   | `docs/domain/ubiquitous-language.md`                                    |

## Phase 1: Contract & Type System Cleanup (High Impact, Low Effort)

**Root causes addressed**: Contract/type mismatches (PRs #76, #78), unsafe type assertions

### 1.1 Fix Risk Type Duplication

**Plan exists**: `docs/plans/fix-risk-type-duplication.md` (7 steps, already written)

- [ ] Execute steps 1-7 of the existing plan
- [ ] Verify: zero `as unknown as` casts in `apps/web/src/features/risks/`
- [ ] Verify: zero imports from `@pactosigna/shared` response types in frontend

**Estimated scope**: ~8 files across 3 packages

### 1.2 Audit All Contract Boundaries

Systematically check every API client file for type mismatches:

- [ ] `apps/web/src/shared/services/api/*.ts` -- every function must use types from `@pactosigna/contracts`
- [ ] Identify any remaining `Extended*Response` types in frontend code
- [ ] Identify any `as unknown as` casts (each one indicates a contract mismatch)
- [ ] For each mismatch: fix the contract, not the frontend

**Create GitHub issues for each mismatch found.**

### 1.3 Add CI Guard for Package Boundaries

- [ ] Add an ESLint rule or build check that prevents `@pactosigna/web` from importing `@pactosigna/data`
- [ ] Add a lint rule that flags `as unknown as` patterns for review

## Phase 2: Frontend Architecture Migration (High Impact, Medium Effort)

**Root causes addressed**: Frontend state race conditions (PRs #60, #63, #72), large God Components

### 2.1 Extract ViewModel Hooks for Highest-Impact Pages

Prioritized by page size and bug history:

| Priority | Page                           | Lines | Target Hook                 | Bug History               |
| -------- | ------------------------------ | ----- | --------------------------- | ------------------------- |
| 1        | `ReleaseDetailPage.tsx`        | 1,148 | `useReleaseDetail()`        | PR #72 race condition     |
| 2        | `DocumentDetailPage.tsx`       | 595   | `useDocumentDetail()`       | PR #76 type mismatch      |
| 3        | `OrganizationSettingsPage.tsx` | 848   | `useOrganizationSettings()` | None, but high complexity |

For each page:

- [ ] Create `features/{name}/hooks/use{PageName}.ts`
- [ ] Move all data fetching into the hook using `useQuery()`
- [ ] Move derived state (useMemo) into the hook
- [ ] Leave only rendering in the page component
- [ ] Delete any `Extended*Response` types
- [ ] Add unit tests for the hook

### 2.2 Document Context Interaction Patterns

The SDD Frontend Architecture documents the provider chain and loading protocol. But individual context files lack inline documentation:

- [ ] Add JSDoc comments to `AuthContext.tsx` explaining the loading contract
- [ ] Add JSDoc comments to `OrganizationContext.tsx` explaining why it waits for `authLoading`
- [ ] Add JSDoc comments to `RepositoryContext.tsx` explaining its independence from auth

### 2.3 Standardize Feature Module Structure

For features that are missing hooks/ or components/ directories:

| Feature         | Missing             | Action                                 |
| --------------- | ------------------- | -------------------------------------- |
| repositories    | hooks/              | Create when next touching this feature |
| documents       | hooks/, components/ | Phase 2.1 creates hooks/               |
| releases        | hooks/              | Phase 2.1 creates hooks/               |
| change-controls | hooks/, components/ | Create when next touching              |
| risks           | hooks/              | Created as part of Phase 1.1           |
| settings        | hooks/              | Phase 2.1 creates hooks/               |
| audit           | hooks/              | Create when next touching              |

**Rule**: Don't create empty directories. Create hooks/ when you extract the first ViewModel hook for that feature.

## Phase 3: Backend Robustness (Medium Impact, Medium Effort)

**Root causes addressed**: Document ID confusion (PRs #64, #66), Firestore write errors

### 3.1 Add findByDocumentId Guard

The Document ID vs Firestore ID confusion caused multiple bugs. Prevent recurrence:

- [x] Add a JSDoc comment to `DocumentsRepository.findById()` warning that this uses the Firestore ID
- [x] Add a JSDoc comment to `DocumentsRepository.findByDocumentId()` noting this is for URL-sourced IDs
- [x] Audit all service methods that receive IDs from URL params -- verify they use `findByDocumentId()`
- [x] Add ESLint `no-restricted-syntax` rule to block `documentsRepo.findById()` calls (apps/services + apps/functions)
- [x] Fix ambiguous test data in ReleasesService.test.ts and ChangeControlsService.test.ts (distinct Firestore IDs vs frontmatter IDs)

### 3.2 Firestore Write Safety

- [ ] Create a shared utility `stripUndefined(obj)` to sanitize objects before Firestore writes
- [ ] Audit repository `create()` and `update()` methods for potential `undefined` values
- [ ] Add a unit test that verifies no repository passes `undefined` to Firestore

### 3.3 Collection Group Index Verification

- [ ] Create a script or CI check that compares `collectionGroup()` calls in code against entries in `firebase/firestore.indexes.json`
- [ ] Document in SDD Backend that emulator does NOT enforce index requirements

## Phase 4: Testing & UX Documentation (Medium Impact, Ongoing)

**Root causes addressed**: Missing test coverage, missing UX documentation

### 4.1 UX Documentation

Currently `docs/ux/` only has training-related docs (5% of the product). Create UX artifacts for core flows:

| Epic                | UX Doc Needed                                             | Priority |
| ------------------- | --------------------------------------------------------- | -------- |
| Document Management | User journey: sync, browse, view detail, traceability     | High     |
| Release Management  | User journey: create release, collect signatures, publish | High     |
| Risk Management     | User journey: view matrix, assess gaps, link mitigations  | Medium   |
| Organization Setup  | User journey: create org, invite members, connect repo    | Medium   |
| Audit & Compliance  | User journey: view audit log, filter, export              | Low      |

For each:

- [ ] Create `docs/ux/{epic}/user-journey.md` with step-by-step flows
- [ ] Create `docs/ux/{epic}/terminology.md` with user-facing labels
- [ ] Reference from `docs/ux/README.md`

### 4.2 Increase Unit Test Coverage for Bug-Prone Areas

Priority test targets based on bug frequency:

- [ ] `DocumentsService` -- test `getDocumentDetails()` and `getDocumentContent()` with both ID types
- [ ] `RisksService` -- test response shape matches contracts exactly
- [ ] `ReleaseDetailPage` -- test hook isolation after Phase 2.1 extraction
- [ ] Context loading protocol -- test that `OrganizationProvider` stays loading when `authLoading=true`

### 4.3 E2E Coverage for Critical Paths

- [ ] Document sync journey (sync → verify documents appear)
- [ ] Release lifecycle (create → sign → publish)
- [ ] Training completion (receive task → take quiz → complete)

## Phase 5: Process Improvements (Low Effort, High Prevention)

### 5.1 PR Checklist

Add to the GitHub PR template:

```markdown
## Checklist

- [ ] Types come from `@pactosigna/contracts` (not custom frontend types)
- [ ] No `as unknown as` casts introduced
- [ ] If new page: uses ViewModel hook pattern (not useState+useEffect)
- [ ] If new Firestore query: index added to `firestore.indexes.json`
- [ ] If new secret: added to function's `secrets` array
- [ ] If URL param used for document lookup: uses `findByDocumentId()`
- [ ] `pnpm typecheck` passes
- [ ] `pnpm format:check` passes
```

### 5.2 ADR for Frontend Architecture

The SDD Frontend Architecture document captures the current state. Formalize the key decisions as ADRs:

- [ ] **ADR-020**: ViewModel Hook pattern as the required data fetching approach
- [ ] **ADR-021**: App Shell loading protocol (parent must resolve before child proceeds)

### 5.3 Periodic Architecture Review

Schedule a monthly review of:

- New `as unknown as` casts introduced
- Pages exceeding 300 lines
- Features missing hooks/ directories
- Documentation currency (are HLD/SDD still accurate?)

## Execution Priority

| Phase                          | When                                    | Prerequisites                            |
| ------------------------------ | --------------------------------------- | ---------------------------------------- |
| Phase 1 (Contracts)            | **Next sprint**                         | Merge this documentation branch first    |
| Phase 2.1 (Top 3 pages)        | **Next sprint** (parallel with Phase 1) | None                                     |
| Phase 3.1-3.2 (Backend guards) | **Next sprint**                         | None                                     |
| Phase 5.1 (PR checklist)       | **Immediately**                         | None                                     |
| Phase 2.2-2.3 (Context docs)   | **Ongoing**                             | When touching features                   |
| Phase 4 (UX docs + tests)      | **Backlog**                             | Create GitHub issues, work incrementally |
| Phase 5.2-5.3 (ADRs + reviews) | **After Phase 1-2**                     | Patterns stabilized                      |

## Success Metrics

After executing this plan:

1. **Zero `as unknown as` casts** in frontend risk, release, and document features
2. **Top 3 pages under 200 lines** each (down from 595-1,148)
3. **All features have hooks/** directory with ViewModel hooks
4. **UX documentation** exists for all 6 core epics
5. **PR checklist** catches type and architecture issues before merge
6. **No repeat bugs** from document ID confusion or context race conditions
