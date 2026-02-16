# Five Bug Fixes Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Fix five production bugs — missing Firestore indexes, redundant/broken risk matrix UI, traceability crash, dead reviewer-rules settings tab, and dashboard 500 error.

**Architecture:** All fixes are frontend or infra (Firestore indexes). No new backend endpoints needed. The risk matrix page switches from generic org-wide API to existing device-scoped API. The settings page removes dead UI. Firestore indexes are added for missing queries.

**Tech Stack:** React 19, Vitest, Firestore indexes (JSON), Fastify backend

---

## Task 1: Add Missing Firestore Composite Index for Traceability (Bug 3 + Bug 5)

The traceability gaps endpoint crashes with `FAILED_PRECONDITION: The query requires an index` because `DocumentsRepository.findByType()` queries `collectionGroup('documents')` with `.where('organizationId', '==', orgId).where('docType', '==', docType).where('status', '!=', 'obsolete')` — this 3-field query (with inequality on `status`) requires a composite index that is missing.

Bug 5 (dashboard 500) has the index already defined in `firestore.indexes.json` but it wasn't deployed. We will verify this and add the missing traceability index.

**Files:**

- Modify: `firebase/firestore.indexes.json` (add composite index after line 141)
- Test: `packages/data/src/repositories/DocumentsRepository.test.ts` (add test for findByType query shape)

**Step 1: Write a test that documents the expected query shape for findByType**

Add to `packages/data/src/repositories/DocumentsRepository.test.ts`:

```typescript
describe('findByType', () => {
  it('should query collection group with organizationId, docType, and status filter when no repositoryId', async () => {
    // This documents the query that requires composite index:
    // collectionGroup('documents')
    //   .where('organizationId', '==', orgId)
    //   .where('docType', '==', docType)
    //   .where('status', '!=', 'obsolete')
    const mockSnapshot = { docs: [] };
    const mockQuery = {
      where: vi.fn().mockReturnThis(),
      get: vi.fn().mockResolvedValue(mockSnapshot),
    };
    mockDb.collectionGroup.mockReturnValue(mockQuery);

    await repository.findByType('org-1', 'requirement');

    // Verify the query chain requires these three fields
    expect(mockDb.collectionGroup).toHaveBeenCalledWith('documents');
    expect(mockQuery.where).toHaveBeenCalledWith('organizationId', '==', 'org-1');
    expect(mockQuery.where).toHaveBeenCalledWith('docType', '==', 'requirement');
    expect(mockQuery.where).toHaveBeenCalledWith('status', '!=', 'obsolete');
  });
});
```

**Step 2: Run the test to verify it fails or passes (establishes baseline)**

Run: `pnpm --filter @pactosigna/data test -- --run DocumentsRepository`

**Step 3: Add the missing composite index to firestore.indexes.json**

In `firebase/firestore.indexes.json`, add after the existing `(organizationId, docType)` index (after line 141):

```json
{
  "collectionGroup": "documents",
  "queryScope": "COLLECTION_GROUP",
  "fields": [
    { "fieldPath": "docType", "order": "ASCENDING" },
    { "fieldPath": "organizationId", "order": "ASCENDING" },
    { "fieldPath": "status", "order": "ASCENDING" }
  ]
}
```

**Step 4: Verify the index file is valid JSON**

Run: `node -e "JSON.parse(require('fs').readFileSync('firebase/firestore.indexes.json', 'utf8')); console.log('Valid JSON')"`

**Step 5: Commit**

```bash
git add firebase/firestore.indexes.json packages/data/src/repositories/DocumentsRepository.test.ts
git commit -m "fix: add missing Firestore composite index for traceability gaps query

The traceability gaps endpoint crashes with FAILED_PRECONDITION because
DocumentsRepository.findByType() queries (docType + organizationId + status)
on the documents collection group, which requires a composite index."
```

---

## Task 2: Fix Risk Matrix Page — Remove Redundant Repository Selector (Bug 1)

The `RiskMatrixPage` is only mounted at `/devices/:deviceId/risks/matrix` but ignores `deviceId` from the URL entirely. Instead, it fetches all repositories and renders its own `<Select>` dropdown, duplicating the sidebar's repository selection. The fix: read `deviceId` from URL params and remove the repository dropdown.

**Files:**

- Modify: `apps/web/src/features/risks/pages/RiskMatrixPage.tsx`

**Step 1: Write a test that the page reads deviceId from URL params (no repo selector rendered)**

This is a React component — testing it with Vitest + React Testing Library would require a complex setup. Instead, the verification is structural: remove the repository selector UI and the `getConnectedRepositories` call, add `useParams()`. We'll verify via typecheck + existing tests.

**Step 2: Modify RiskMatrixPage.tsx**

Remove:

- `import { getConnectedRepositories } from '@/shared/services/api/repositories-api';` (line 19)
- `RepositoryOption` interface (lines 26-29)
- `repositories` state: `const [repositories, setRepositories] = useState<RepositoryOption[]>([]);` (line 38)
- `selectedRepositoryId` from URL search params: `const selectedRepositoryId = searchParams.get('repositoryId') || '';` (line 44)
- The `useEffect` that fetches repositories (lines 47-58)
- `handleRepositoryChange` handler (lines 119-127)
- The entire `<FormControl>` for repository selector (lines 160-175)
- The `showRepositoryColumn` variable (line 150)
- Unused imports: `FormControl`, `InputLabel`, `Select`, `MenuItem`

Add:

- `import { useParams } from 'react-router-dom';`
- `const { deviceId } = useParams<{ deviceId: string }>();`
- Use `deviceId` as the fixed repositoryId for API calls

The page now always calls `getRiskMatrix({ organizationId, repositoryId: deviceId, type: ... })` and `getRiskGaps({ organizationId, repositoryId: deviceId })`. The repository is determined by the URL, matching the sidebar selection.

**Step 3: Run typecheck and lint**

Run: `pnpm typecheck && pnpm lint`

**Step 4: Commit**

```bash
git add apps/web/src/features/risks/pages/RiskMatrixPage.tsx
git commit -m "fix: remove redundant repository selector from risk matrix page

RiskMatrixPage is mounted at /devices/:deviceId/risks/matrix but was
ignoring deviceId and showing its own repo dropdown. Now reads deviceId
from URL params, matching the sidebar's repository selection."
```

---

## Task 3: Fix Risk Matrix Filter — Pass riskType to Gaps Query (Bug 2)

The type filter toggles correctly update `getRiskMatrix()` (which accepts `type` param), but `getRiskGaps()` never receives or passes `riskType`. The backend's `findAllRiskDocuments()` also doesn't accept `riskType`. This makes the gaps section always show all risk types regardless of filter.

**Files:**

- Modify: `packages/data/src/repositories/RisksRepository.ts` (add `riskType` to `findAllRiskDocuments`)
- Modify: `apps/services/src/modules/risks/RisksService.ts` (pass `riskType` through to gaps)
- Modify: `apps/web/src/features/risks/pages/RiskMatrixPage.tsx` (pass `type` to `getRiskGaps`)
- Test: `apps/services/src/modules/risks/RisksService.test.ts` (add test for riskType filtering in gaps)

**Step 1: Write a failing test in RisksService.test.ts**

Add to the `getRiskGaps` describe block:

```typescript
it('should filter by risk type when provided', async () => {
  mockRepository.verifyMembership.mockResolvedValue(undefined);
  mockRepository.findAllRiskDocuments.mockResolvedValue([]);
  mockRepository.findLinks.mockResolvedValue([]);
  mockRepository.findRepositoryNames.mockResolvedValue(new Map());

  await service.getRiskGaps('user-1', {
    organizationId: 'org-1',
    riskType: 'software_risk',
  });

  expect(mockRepository.findAllRiskDocuments).toHaveBeenCalledWith('org-1', {
    repositoryId: undefined,
    riskType: 'software_risk',
  });
});
```

**Step 2: Run the test to verify it fails**

Run: `pnpm --filter @pactosigna/services test -- --run RisksService`
Expected: FAIL — `findAllRiskDocuments` called with `{ repositoryId: undefined }` (missing `riskType`)

**Step 3: Update RisksRepository.findAllRiskDocuments to accept riskType**

In `packages/data/src/repositories/RisksRepository.ts`, change `findAllRiskDocuments`:

```typescript
async findAllRiskDocuments(
  organizationId: string,
  options?: { repositoryId?: string; riskType?: string }
): Promise<RiskDocument[]> {
  const riskTypes = options?.riskType
    ? [options.riskType]
    : RISK_TYPES;

  let query: FirebaseFirestore.Query;

  if (options?.repositoryId) {
    query = this.repositoryDocumentsRef(organizationId, options.repositoryId).where(
      'docType',
      'in',
      riskTypes
    );
  } else {
    query = this.documentsCollectionGroup()
      .where('organizationId', '==', organizationId)
      .where('docType', 'in', riskTypes);
  }

  const snapshot = await query.get();
  return snapshot.docs.map(doc =>
    this.mapToRiskDocument(doc.id, doc.data() as RiskDocumentModel)
  );
}
```

**Step 4: Update RisksService.getRiskGaps to accept and pass riskType**

In `apps/services/src/modules/risks/RisksService.ts`, change `getRiskGaps`:

```typescript
async getRiskGaps(
  userId: string,
  params: { organizationId: string; repositoryId?: string; riskType?: string }
): Promise<RiskGapsResponse> {
  const { organizationId, repositoryId, riskType } = params;

  await this.repository.verifyMembership(organizationId, userId);

  const [documents, links, repoNames] = await Promise.all([
    this.repository.findAllRiskDocuments(organizationId, { repositoryId, riskType }),
    // ... rest unchanged
  ]);
```

**Step 5: Update RiskMatrixPage to pass type to getRiskGaps**

In `apps/web/src/features/risks/pages/RiskMatrixPage.tsx`, change the `getRiskGaps` call:

```typescript
getRiskGaps({
  organizationId,
  repositoryId: deviceId,
  type: (selectedType as 'software_risk' | 'usability_risk' | 'security_risk') || undefined,
}),
```

Note: This also requires updating the `GetRiskGapsQuery` contract to accept `type`. Check `packages/contracts/src/risks/requests.ts` — if `type` isn't in the schema, add it as optional.

**Step 6: Run the test to verify it passes**

Run: `pnpm --filter @pactosigna/services test -- --run RisksService`
Expected: PASS

**Step 7: Run full test suite**

Run: `pnpm test`

**Step 8: Commit**

```bash
git add packages/data/src/repositories/RisksRepository.ts apps/services/src/modules/risks/RisksService.ts apps/web/src/features/risks/pages/RiskMatrixPage.tsx packages/contracts/src/risks/requests.ts
git commit -m "fix: pass riskType filter through to risk gaps query

The risk matrix type filter updated the heatmap but not the gaps section
because getRiskGaps never received or forwarded the riskType parameter.
Now findAllRiskDocuments accepts riskType and the full chain is connected."
```

---

## Task 4: Remove Dead Reviewer Rules Tab from Settings (Bug 4)

The "Reviewer Rules" tab on `OrganizationSettingsPage` lets admins configure per-document-type reviewer rules, but these rules are never read anywhere in the release/signature workflow. Releases take `requiredDepartments` as user input at creation time. The tab gives admins a false sense of control. Reviewers are actually defined in document frontmatter (`reviewers`, `approvers` fields).

**Files:**

- Modify: `apps/web/src/features/settings/pages/OrganizationSettingsPage.tsx`
- Modify: `docs/architecture/adr-005-department-based-permissions.md` (add note about current reality)

**Step 1: Remove the Reviewer Rules tab and all associated code from OrganizationSettingsPage.tsx**

Remove:

- `ReviewerRulesRecord` type (lines 66-69)
- `rulesToRecord` function (lines 71-80)
- `recordToRules` function (lines 82-88)
- `reviewerRulesFromOrg` memo (lines 208-212)
- `initialReviewerRules` memo (lines 215-218)
- `reviewerRules` state (line 221)
- `reviewerRules` sync effect (lines 224-226)
- `handleReviewerRuleChange` handler (lines 287-299)
- `saveReviewerRules` handler (lines 301-322)
- The entire `<Tab label="Reviewer Rules" />` from the Tabs component (line 487)
- The entire `<TabPanel value={tabValue} index={2}>` block (lines 622-722)
- Unused imports: `DEFAULT_REVIEWER_RULES`, `DOCUMENT_TYPES`, `ReviewerRule`, `updateOrganizationSettings` (if only used for reviewer rules — check first)

Update tab indices:

- `TAB_INDICES` map (lines 130-137): remove `'reviewer-rules': 2`, renumber `members: 2`, `invites: 3`, `repositories: 4`
- `TabPanel` indices for Members (was 3 → 2), Pending Invites (was 4 → 3), Repositories (was 5 → 4)

**Step 2: Update ADR-005 to reflect reality**

Add a section after "Consequences":

```markdown
## Status Update (2026-02-05)

The organization-level reviewer rules described above were never wired into the
release or signature workflow. Instead:

- **Release creation** accepts `requiredDepartments` directly from the user
- **Document reviewers/approvers** are defined in markdown frontmatter
- The Settings UI "Reviewer Rules" tab was removed as dead functionality

If organization-level reviewer rule enforcement is needed in the future, it should
be implemented as validation in `ReleasesService.createRelease()` that cross-references
org settings when determining required signers.
```

**Step 3: Run typecheck and lint**

Run: `pnpm typecheck && pnpm lint`

**Step 4: Run tests**

Run: `pnpm test`

**Step 5: Commit**

```bash
git add apps/web/src/features/settings/pages/OrganizationSettingsPage.tsx docs/architecture/adr-005-department-based-permissions.md
git commit -m "fix: remove dead Reviewer Rules tab from organization settings

The Reviewer Rules tab stored config that was never read by the release
or signature workflow. Reviewers are defined in document frontmatter.
Removed the misleading UI and updated ADR-005 to document reality."
```

---

## Task 5: Deploy Firestore Indexes (Bug 5 completion)

The dashboard 500 error on `/api/v2/documents?limit=1` is caused by missing Firestore indexes in production. The indexes are defined in `firestore.indexes.json` (from the earlier bugfix branch) but haven't been deployed.

**Step 1: Deploy indexes to production**

Run: `firebase deploy --only firestore:indexes`

This deploys all indexes defined in `firestore.indexes.json` including:

- `(organizationId, lastSyncedAt)` — fixes dashboard documents query
- `(organizationId, docType, lastSyncedAt)` — fixes filtered document queries
- `(organizationId, status, lastSyncedAt)` — fixes status-filtered queries
- `(docType, organizationId, status)` — fixes traceability gaps query (added in Task 1)

**Step 2: Verify indexes are building**

Run: `firebase firestore:indexes` to list current index status.

Note: Composite indexes can take several minutes to build in production. Monitor the Firebase console.

**Step 3: Verify the dashboard loads without 500 errors**

Navigate to `https://qms.pactosigna.com/dashboard` and check browser console for errors.

**Step 4: Verify traceability page loads without errors**

Navigate to the traceability page and confirm it loads data.

---

## Task 6: Final Verification

**Step 1: Run full test suite**

Run: `pnpm test`

**Step 2: Run typecheck**

Run: `pnpm typecheck`

**Step 3: Run format check**

Run: `pnpm format:check`
If issues: `pnpm format`

**Step 4: Verify all 5 bugs are addressed**

| Bug                                       | Fix                                                         | Verification                                |
| ----------------------------------------- | ----------------------------------------------------------- | ------------------------------------------- |
| 1. Redundant repo selector on risk matrix | Removed selector, uses deviceId from URL                    | Page has no repo dropdown                   |
| 2. Risk matrix filter doesn't update gaps | riskType passed through to findAllRiskDocuments             | Toggle filter updates both heatmap and gaps |
| 3. Traceability 400/500 error             | Added composite index for (docType, organizationId, status) | Page loads without error                    |
| 4. Settings shows dead Reviewer Rules     | Tab removed, ADR-005 updated                                | Settings has no Reviewer Rules tab          |
| 5. Dashboard 500 on documents API         | Indexes deployed to production                              | Dashboard loads without console errors      |
