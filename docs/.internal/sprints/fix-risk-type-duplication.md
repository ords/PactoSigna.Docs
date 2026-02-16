# Fix: Risk Type Duplication Between Shared and Contracts

## Problem

`RiskMatrixResponse`, `RiskGapsResponse`, `RiskSummarySchema`, and `RiskGapSchema` exist in both
`@pactosigna/shared` and `@pactosigna/contracts` with different field names and shapes. This
causes `as unknown as` casts, fabricated fields, and a manual translation layer in `RiskMatrixPage`.

See ADR-014 "Violations Found" section for full root cause analysis.

## Scope

### Step 1: Make contracts `AcceptabilityStatusSchema` import from shared

**Why:** Both packages declare `AcceptabilityStatusSchema` independently. Shared is the correct
single source of truth for domain enums (per ADR-014). Contracts should re-export, not redeclare.

**Files:**

| File                                        | Change                                                                                                                                                            |
| ------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `packages/contracts/src/risks/responses.ts` | Remove local `AcceptabilityStatusSchema` declaration (lines 6-8). Import `AcceptabilityStatusSchema` and `AcceptabilityStatus` from `@pactosigna/shared` instead. |

**Verification:** `pnpm typecheck` passes. No runtime change.

### Step 2: Migrate frontend components from shared to contracts types

**Why:** Components currently import response DTOs from shared, forcing the page to translate
between contracts (what the API returns) and shared (what the components expect).

**Files:**

| File                                                           | Current Import                                                                            | New Import                                         | Notes                                                                         |
| -------------------------------------------------------------- | ----------------------------------------------------------------------------------------- | -------------------------------------------------- | ----------------------------------------------------------------------------- |
| `apps/web/src/features/risks/components/RiskHeatmap.tsx`       | `AcceptabilityStatus` from `@pactosigna/shared`                                           | `AcceptabilityStatus` from `@pactosigna/contracts` | Type is structurally identical, no logic change                               |
| `apps/web/src/features/risks/components/RiskGapsAccordion.tsx` | `RiskGapsResponse` from `@pactosigna/shared`                                              | `RiskGapsResponse` from `@pactosigna/contracts`    | Component only uses `.gaps` (flat array), never `.byType`, so no logic change |
| `apps/web/src/features/risks/pages/RiskMatrixPage.tsx`         | `RiskMatrixResponse`, `RiskGapsResponse`, `AcceptabilityStatus` from `@pactosigna/shared` | Same types from `@pactosigna/contracts`            | See Step 3                                                                    |

**Verification:** `pnpm typecheck` passes. Components render identically.

### Step 3: Remove the translation layer in RiskMatrixPage

**Why:** Once components use contracts types, the page no longer needs to translate between the
two type systems. The API client already returns contracts types.

**File:** `apps/web/src/features/risks/pages/RiskMatrixPage.tsx`

> Note: As of the latest merge from main, this page is now device-scoped (receives `deviceId`
> from URL params via `useParams`). The repository selector was removed. The translation layer
> and type issue remain identical.

**Current code (lines 56-68):**

```typescript
// Transform the API response (contracts types) to match shared RiskMatrixResponse
setMatrixData({
  labels: matrixResult.labels,
  inherentMatrix: matrixResult.inherent,
  residualMatrix: matrixResult.residual,
  acceptabilityMatrix: matrixResult.acceptability as unknown as string[][],
  summary: {
    ...matrixResult.summary,
    withMitigations: 0,
    gapsCount: 0,
  },
});
setGapsData(gapsResult as unknown as RiskGapsResponse);
```

**Replace with:**

```typescript
setMatrixData(matrixResult);
setGapsData(gapsResult);
```

**Additional changes in the same file:**

- Line 27: `useState<RiskMatrixResponse>` — no change needed (type changes via import)
- Line 28: `useState<RiskGapsResponse>` — no change needed
- Line 114: Remove `const typedAcceptabilityMatrix = matrixData.acceptabilityMatrix as AcceptabilityStatus[][];` — contracts type already has `AcceptabilityStatus[][]`, no cast needed
- Line 163: Change `matrixData.inherentMatrix` → `matrixData.inherent`
- Line 175: Change `matrixData.residualMatrix` → `matrixData.residual`
- Lines 164, 177: Change `acceptabilityMatrix={typedAcceptabilityMatrix}` → `acceptabilityMatrix={matrixData.acceptability}`

**Verification:** `pnpm typecheck` passes. No `as unknown as` casts remain. Page renders identically.

### Step 4: Update RiskHeatmap and RiskGapsAccordion prop types (if needed)

Verify that the prop types for these components align with contracts types. Specifically:

- `RiskHeatmap` expects `matrix: number[][]` and `acceptabilityMatrix: AcceptabilityStatus[][]` — these match contracts' `inherent`/`residual` (both `number[][]`) and `acceptability` (`AcceptabilityStatus[][]`). No change needed.
- `RiskGapsAccordion` expects `gapsData: RiskGapsResponse | null` — once the import changes, this aligns with contracts' shape. The component only accesses `.gaps` (the array), not `.byType`. No change needed.

### Step 5: Delete response DTOs from shared

**Why:** With no consumers left, the response DTOs can be safely removed.

**File:** `packages/shared/src/schemas/risk.ts`

Delete lines 118-165 (everything from `// API response types` to the end of the file):

- `RiskGapSchema` (the Zod schema — not to be confused with the `RiskGapCode`/`RiskGapSeverity` enums which stay)
- `RiskSummarySchema`
- `RiskMatrixResponseSchema`
- `RiskGapsResponseSchema`
- Inferred types: `RiskGap`, `RiskSummary`, `RiskMatrixResponse`, `RiskGapsResponse`

**Dependency to fix first:** `packages/shared/src/types/snapshot.ts:10` imports `RiskGap` from `../schemas/risk.js`. This must be replaced with a local interface:

```typescript
// In snapshot.ts, replace:
import type { RiskGap } from '../schemas/risk.js';

// With an inline interface using the legitimate shared enums:
import type { RiskGapCode, RiskGapSeverity } from './common.js';

interface RiskGap {
  code: RiskGapCode;
  severity: RiskGapSeverity;
  documentId: string;
  documentTitle: string;
  message: string;
  repositoryId?: string;
  repositoryName?: string;
}
```

**Verification:** `pnpm typecheck` passes. `pnpm --filter @pactosigna/shared test` passes.

### Step 6: Clean up backend dead code

**File:** `apps/services/src/modules/risks/riskMatrix.ts`

Remove `withMitigations` and `gapsCount` from the internal `RiskMatrixResult` interface (lines 19-20)
and from the return value (lines 100-101). These are always `0` and are stripped by `RisksService`
before building the contracts response.

**Before:**

```typescript
interface RiskMatrixResult {
  // ...
  summary: {
    total: number;
    acceptable: number;
    reviewRequired: number;
    unacceptable: number;
    withMitigations: number; // DELETE
    gapsCount: number; // DELETE
  };
}
```

**After:**

```typescript
interface RiskMatrixResult {
  // ...
  summary: {
    total: number;
    acceptable: number;
    reviewRequired: number;
    unacceptable: number;
  };
}
```

And remove lines 100-101 from the `return` statement:

```typescript
// DELETE these two lines:
withMitigations: 0,
gapsCount: 0,
```

**Also:** `RisksService.ts:40-45` currently destructures `matrix.summary` fields individually to
exclude `withMitigations`/`gapsCount`. Once those are removed from `RiskMatrixResult`, the service
can pass `summary: matrix.summary` directly.

> Note: As of the latest merge from main, `getRiskGaps` now also accepts a `riskType` parameter
> and passes it through to `findAllRiskDocuments`. This is unrelated to the type duplication fix.

**Verification:** `pnpm --filter @pactosigna/services test` passes.

### Step 7: Remove unused `groupGapsByType` function

**File:** `apps/services/src/modules/risks/riskGaps.ts`

`groupGapsByType()` (line 192) returns `Record<string, number>` — it counts gaps per type. But
`RisksService.getRiskGaps()` does its own grouping at lines 95-102 that returns
`Record<string, RiskGap[]>` (arrays per type, matching contracts). The function is exported but
never called from production code — only from its own test file.

Delete `groupGapsByType` from `riskGaps.ts` and remove its tests from `riskGaps.test.ts`.

**Verification:** `pnpm --filter @pactosigna/services test` passes.

## Verification Plan

After all steps:

1. `pnpm typecheck` — zero errors across all workspaces
2. `pnpm test` — all unit tests pass
3. `pnpm --filter @pactosigna/web build` — frontend builds cleanly
4. Manual check: zero `as unknown as` casts in `apps/web/src/features/risks/`
5. Manual check: zero imports from `@pactosigna/shared` in `apps/web/src/features/risks/` (except domain enums if used directly)
6. `grep -r "RiskMatrixResponseSchema\|RiskGapsResponseSchema\|RiskSummarySchema" packages/shared/` returns nothing

## Out of Scope (Phase 3)

The following items are related but deliberately excluded from this fix:

- **Entity interfaces in `shared/src/types/`**: `RiskListItem`, `RiskDetail`, `RiskGraph`, `RiskSnapshot` in `types/risk.ts`; `Release`, `Signature`, `ReleaseSnapshot` in `types/release.ts`; `ProductRelease`, `DocumentSnapshot` in `types/product-release.ts`. These are the broader ADR-014 migration debt.
- **Snapshot types** (`types/snapshot.ts`): `RepositorySnapshot`, `RiskMatrixSnapshot` etc. These are used by the sync engine and release publish flow — migrating them requires touching more of the backend.
- **Contracts types used by `apps/functions`**: `attestation.ts` and `legacy-release.ts` import from shared — these need separate migration work.
