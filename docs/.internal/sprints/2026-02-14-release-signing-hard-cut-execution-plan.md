# Release Signing Hard-Cut Execution Plan

Date: 2026-02-14
Status: Proposed (implementation-ready)
Scope: Release Management signing model only
Compatibility: **No backward compatibility** (pre-launch hard cut)

## 1) Objective

Replace the current release-level `requiredDepartments` model with a server-derived obligation model based on documents changed since the last published release.

Target derivation inputs per changed document:

- `owner` (email)
- `reviewers` (department list)
- `approvers` (department list)

Target release behavior:

- UI does not provide signer requirements.
- API derives signer obligations server-side.
- Signatures fulfill specific obligations.
- Release auto-approves only when all obligations are fulfilled.

## 2) Locked Decisions

1. Server is single source of truth for signer requirements.
2. Client-provided signer policy (`requiredDepartments`, ad-hoc role selection) is removed.
3. Derivation uses only changed documents since last published release.
4. Submit-for-review snapshots obligations for that release instance.
5. In-review obligations do not drift with later metadata/settings edits.
6. No legacy compatibility paths are preserved.

## Decision Lock

1. Owner membership gate: submit-for-review is blocked if any changed document owner email is not an active organization member.
2. Approver cardinality: each changed document must declare exactly one approver department.
3. Obligation dedupe: obligations merge by `(role, principal)` across changed documents and retain source evidence lists (`documentIds`, `commitShas`).
4. Recall/resubmit behavior: resubmit regenerates obligations from the latest changed-document snapshot.
5. No published baseline rule: when no prior published release exists, all currently synced in-scope documents are treated as changed.

## 3) Hard-Cut Contract Changes (Breaking)

### Remove

- `requiredDepartments` from release create/update request schemas.
- `requiredDepartments` from release summary/detail responses.
- Release-signature request authority via free-form `signatureType`.

### Add

- `signingObligations` in release detail (and summary counters in list responses).
- `obligationId` in add-release-signature request.
- Derivation metadata fields: `obligationsDerivedAt`, `obligationsDerivationVersion`, `obligationsInputHash`.

## 4) Canonical Domain Model

Release persists a snapshot of obligations:

- Role: `owner_email` | `reviewer_department` | `approver_department`
- Principal: `{ email }` or `{ department }`
- Source evidence: contributing `documentIds` + `commitShas`
- Fulfillment state: pending/satisfied + signature reference

Signature records must bind to one obligation:

- `obligationId`
- signer identity snapshot (`userId`, `userEmail`, `userDepartment`)
- `meaning`
- `signedCommitShas`
- immutable timestamp

## 5) File-Level Implementation Plan

## Phase 0 — ADR + Rules Lock (Required)

### Files

- `docs/architecture/adr/adr-005-department-based-permissions.md`
- `docs/architecture/adr/adr-007-hybrid-signature-flow.md`

### Actions

- Supersede department-list approval authority with obligation model.
- Define frontmatter signer semantics explicitly:
  - `author`: email
  - `reviewers`: departments
  - `approvers`: departments
- Define failure policy for malformed/missing signer metadata.

### Exit Criteria

- ADRs explicitly describe obligation-based approval.
- No docs describe client-provided `requiredDepartments` as runtime authority.

---

## Phase 1 — Document Signer Normalization in Sync

### Files

- `apps/functions/src/shared/document-parser.ts`
- `apps/functions/src/shared/sync-engine.ts`
- `apps/functions/src/shared/document-parser.test.ts`
- `apps/functions/src/shared/sync-engine.test.ts`

### Actions

- Parse signer fields as first-class parsed properties (not generic metadata only).
- Persist normalized document signer fields to document records:
  - `author`, `reviewers`, `approvers`
- Validate shape and fail sync for invalid signer metadata.

### Exit Criteria

- Changed documents in Firestore contain normalized signer fields.
- Tests cover valid, missing, malformed signer metadata.

---

## Phase 2 — Data Models + Repository Layer

### Files

- `packages/data/src/models/releaseModels.ts`
- `packages/data/src/models/documentModels.ts` (verify/align field types)
- `packages/data/src/repositories/ReleasesRepository.ts`
- `packages/data/src/repositories/ReleasesRepository.test.ts`
- `packages/data/src/repositories/DocumentsRepository.ts`
- `packages/data/src/repositories/firestore-indexes.test.ts`
- `firebase/firestore.indexes.json` (if query shape changes)

### Actions

- Replace release `requiredDepartments` storage with `signingObligations` snapshot.
- Add repository operations for obligation fulfillment and summaries.
- Add/rework document lookup helpers for derivation without N+1 patterns.
- Add required Firestore composite indexes and index tests.

### Exit Criteria

- Release repository supports obligation lifecycle end-to-end.
- Index tests pass for all new/changed queries.

---

## Phase 3 — Contracts + API Routes (Breaking Cut)

### Files

- `packages/contracts/src/releases/requests.ts`
- `packages/contracts/src/releases/responses.ts`
- `packages/contracts/src/entities.ts`
- `apps/services/src/modules/releases/releasesMutationRoutes.ts`
- `apps/services/src/modules/releases/releaseMappers.ts`

### Actions

- Remove legacy fields from request/response schemas.
- Introduce obligation-based response DTOs.
- Change add-signature route contract to require `obligationId`.

### Exit Criteria

- Old release payloads with `requiredDepartments` fail schema validation.
- New release contracts compile across packages.

---

## Phase 4 — Service Workflow Migration

### Files

- `apps/services/src/modules/releases/ReleaseMutationService.ts`
- `apps/services/src/modules/releases/ReleaseQueryService.ts`
- `apps/services/src/modules/releases/ReleaseSignatureService.ts`
- `apps/services/src/modules/releases/ReleaseWorkflowService.ts`
- `apps/services/src/modules/releases/ReleasesService.ts`
- `apps/services/src/modules/releases/ReleasesService.test.ts`
- `apps/services/src/modules/releases/ReleasesController.test.ts`

### Actions

- Derive obligations server-side from changed documents + normalized signer fields.
- Persist derivation metadata (`derivedAt`, version, input hash).
- Update submit-for-review gate from “has required departments” to “has valid obligations,” including owner active-membership and exactly-one-approver checks per changed document.
- Validate signatures against obligation context:
  - owner role -> email match
  - reviewer/approver roles -> department match
- Deduplicate obligations by `(role, principal)` across changed documents while preserving source evidence lists.
- Fulfill obligations atomically with signature write.
- Auto-approve only when all obligations are satisfied.
- On recall->resubmit, regenerate obligations from the latest changed-document snapshot.

### Exit Criteria

- End-to-end release lifecycle is obligation-driven.
- No code path relies on release-level `requiredDepartments`.

---

## Phase 5 — Frontend Hard Cut

### Files

- `apps/web/src/features/releases/pages/CreateReleasePage.tsx`
- `apps/web/src/features/releases/components/RequiredDepartmentsCard.tsx` (remove)
- `apps/web/src/shared/services/api/releases-api.ts`
- `apps/web/src/features/signatures/hooks/useSignatureDialogState.ts`
- `apps/web/src/features/releases/pages/__tests__/CreateReleasePage.test.tsx`
- `apps/web/src/features/releases/pages/__tests__/ReleaseDetailPage.test.tsx`
- `apps/web/src/shared/services/api/__tests__/releases-api.test.ts`

### Actions

- Remove manual required-departments selection UX.
- Update create-release API call to stop sending signer requirements.
- Show derived obligations and fulfillment state in release detail.
- Update sign flow to select/submit `obligationId` instead of free-form role.

### Exit Criteria

- UI has zero controls for manual release signer policy.
- UI renders server-derived obligations only.

---

## Phase 6 — Hard Data Reset / Cutover

### Actions

- Purge or recreate non-published release records in dev/staging.
- Re-sync repositories so documents have normalized signer fields.
- Do not migrate/maintain legacy release schema.

### Exit Criteria

- All active test data conforms to obligation-based release schema.
- Legacy release schema paths are removed from runtime.

## 6) Quality Gates (Must Pass)

## Compliance / Security

- Signature records immutable and obligation-bound.
- Existence/ownership/context validation for each signature.
- Audit events for derivation, submission, obligation fulfillment, auto-approval, publish.
- No hidden manual override of pending obligations.

## Concurrency / Integrity

- Double-sign race for same obligation: exactly one success.
- Auto-approval transition deterministic under concurrent signatures.
- Signature write + obligation fulfillment + status transition atomicity proven.

## Performance

- No N+1 derivation scans for changed documents.
- P95 release creation and signing latency within agreed budget.

## 7) Test Plan

## Unit

- Derivation correctness (single/multi-doc, overlap, missing metadata).
- Eligibility checks per role/principal.
- Deduping and obligation completion rules.

## Integration

- Create -> submit -> sign -> approve path using new contract.
- Replay/cross-release misuse rejected.

## E2E

- Release lifecycle journey validates derived obligations and completion behavior.

## Contract Regression

- Legacy request shapes rejected.
- New DTOs enforced end-to-end.

## 8) Policy Gap Resolution Status

All previously open migration policy gaps are resolved and locked in this plan.

- Owner not in org: block submit-for-review.
- Approvers cardinality: exactly one approver department per changed document.
- Obligation dedupe: merge by `(role, principal)` across changed documents with source evidence retained.
- Recall/resubmit: regenerate obligations from latest changed-document snapshot.
- No prior published release: treat all currently synced in-scope documents as changed.

## 9) Delivery Sequence and Estimate

- Phase 0: 0.5 day
- Phase 1: 1 day
- Phase 2: 1 day
- Phase 3: 1 day
- Phase 4: 1.5-2 days
- Phase 5: 1-1.5 days
- Phase 6 + stabilization: 0.5-1 day

Estimated total: **6-8 engineering days** (single squad, focused execution).

## 10) Source Inputs

- `docs/plans/2026-02-14-release-signing-model-hard-cut-product-review.md`
- `docs/plans/2026-02-14-release-signing-obligations-migration-architecture.md`
- `docs/plans/2026-02-14-derived-release-signer-obligations-quality-gate.md`
