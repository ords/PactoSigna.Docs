# Release Signing Model Migration (Hard Cut) — Architecture Review

**Date:** 2026-02-14  
**Scope:** Release Management bounded context (internal signatures only)  
**Decision posture:** Server-derived signing obligations as single source of truth; no backward compatibility

## Decision Lock

1. Owner membership gate: submit-for-review is blocked if any changed document owner email is not an active organization member.
2. Approver cardinality: each changed document must declare exactly one approver department.
3. Obligation dedupe: obligations merge by `(role, principal)` across changed documents and retain source evidence lists.
4. Recall/resubmit behavior: resubmit regenerates obligations from the latest changed-document snapshot.
5. No published baseline rule: when no prior published release exists, all currently synced in-scope documents are treated as changed.

## Design Conflict Report (resolved direction)

1. **Current release workflow is department-driven, not document-obligation-driven**
   - `ReleaseModel.requiredDepartments` is currently authoritative.
   - Signature eligibility currently checks only release-required departments.

2. **Document signature fields are not currently first-class in service contracts**
   - `DocumentModel` has `author/reviewers/approvers`, but sync currently stores frontmatter signature data in `metadata`.
   - Release contracts do not expose nor consume normalized document signing fields.

3. **Frontmatter semantics normalization for reviewers/approvers**
   - Existing examples may use emails; hard cut normalizes reviewer/approver principals to **department roles**.
   - Non-conforming frontmatter is invalid for derivation and blocks submit-for-review until corrected.

---

## 1) Canonical Data Model (recommended)

### 1.1 Release aggregate (replace department list with obligation snapshot)

Add/replace fields in `ReleaseModel`:

- Remove: `requiredDepartments: string[]`
- Add: `signingObligations: SigningObligationModel[]`
- Add: `obligationsDerivedAt: Timestamp`
- Add: `obligationsDerivationVersion: number` (start at `1`)
- Add: `obligationsInputHash: string` (hash of changed docs + commit SHAs + normalized obligation inputs)

```ts
type SigningRole = 'owner_email' | 'reviewer_department' | 'approver_department';
type ObligationStatus = 'pending' | 'satisfied';

interface SigningObligationModel {
  id: string; // deterministic hash(role + principal + releaseId)
  role: SigningRole;
  principal: {
    email?: string; // required for owner_email
    department?: string; // required for reviewer/approver roles
  };
  source: {
    documentIds: string[]; // frontmatter IDs contributing to this obligation
    commitShas: string[]; // signed content binding
  };
  status: ObligationStatus;
  satisfiedBySignatureId?: string;
  satisfiedAt?: Timestamp;
}
```

### 1.2 Signature model (bind signatures to obligation, not free-form signatureType)

In release signature subcollection:

- Keep immutable signature record
- Replace free-form role selection with obligation binding

```ts
interface ReleaseSignatureModel {
  id: string;
  releaseId: string;
  organizationId: string;
  obligationId: string; // required context binding
  role: SigningRole; // copied from obligation for audit readability

  signer: {
    userId: string;
    userEmail: string;
    userDisplayName: string;
    userDepartment: string;
  };

  meaning: string; // server-generated or server-validated template
  signedCommitShas: string[];
  timestamp: Timestamp;
}
```

### 1.3 Document signing source contract (hard cut)

Normalize signing fields from frontmatter into persisted document fields (not only metadata):

- `author: string` (email)
- `reviewers: string[]` (department names)
- `approvers: string[]` (department names, exactly one item required)

Hard cut policy: documents not conforming to this shape are invalid for release derivation.

---

## 2) Required API / Contract Changes (breaking)

## 2.1 Release request contracts

### Breaking

- `CreateReleaseRequest`
  - Remove: `requiredDepartments`
  - Add nothing client-provided for signing obligations (server derives)
- `UpdateReleaseRequest`
  - Remove: `requiredDepartments`

## 2.2 Release response contracts

### Breaking

- `ReleaseSummaryResponse`
  - Remove: `requiredDepartments`
  - Add:
    - `obligationsSummary: { total: number; pending: number; satisfied: number }`
- `ReleaseDetailResponse`
  - Add:
    - `signingObligations: SigningObligationResponse[]`
    - `obligationsInputHash`
    - `obligationsDerivationVersion`

## 2.3 Signature mutation contract

### Breaking

- `AddSignatureRequest`
  - Remove: `signatureType`
  - Add: `obligationId: string`
  - Keep `meaning` only if policy allows user-entered meaning; preferred is server-computed meaning by role template.

## 2.4 Required recall/resubmit behavior

- `recall -> resubmit` MUST regenerate obligations from the latest changed-document snapshot.
- The regenerated snapshot MUST replace prior draft obligations before entering `in_review`.
- Regeneration output MUST include updated `obligationsInputHash` and source evidence lists.

---

## 3) Service-layer Algorithm Outline

## 3.1 Obligation derivation (server-side only)

When creating/updating a draft release:

1. Compute changed documents as today.
2. Load each changed document’s normalized signing fields (`author/reviewers/approvers`).
3. Validate each field:
   - `author` is valid email.
   - `reviewers` are non-empty department names.
   - `approvers` contains exactly one non-empty department name.
   - `author` email belongs to an active organization member.
4. Emit raw obligations per document:
   - owner_email(author)
   - reviewer_department(each reviewers item)
   - approver_department(the single approvers item)
5. Merge/deduplicate across docs by `(role, principal)` and append source evidence (`documentIds`, `commitShas`).
6. Persist `signingObligations`, `obligationsDerivedAt`, `obligationsDerivationVersion`, `obligationsInputHash` on release.

## 3.2 Submit-for-review validation

Before `draft -> in_review`:

- If no prior published release exists, compute changed documents as all currently synced in-scope documents.
- Ensure obligations exist and at least one pending obligation exists.
- Freeze obligation snapshot semantics for in-review (no mutation except `status/satisfied*` fields).

## 3.3 Signature validation flow

On `addSignature(obligationId)`:

1. Validate release exists and is `in_review`.
2. Validate obligation exists on that release and is `pending`.
3. Validate signer membership and role-specific eligibility:
   - `owner_email`: signer email must equal obligation email.
   - `reviewer_department`: signer department must equal obligation department.
   - `approver_department`: signer department must equal obligation department.
4. Enforce uniqueness:
   - one signature per obligation,
   - one user cannot satisfy same obligation twice.
5. Create immutable signature record bound to `obligationId` and current release commit set.
6. Mark obligation satisfied atomically (`satisfiedBySignatureId`, `satisfiedAt`).
7. If all obligations satisfied, transition release to `approved`.

## 3.4 Publish validation

- Keep current explicit publish action (`approved -> published`) and admin authorization.
- No additional ad hoc signature checks at publish; publish relies on already satisfied obligations.

---

## 4) Critical Part 11 / Audit Integrity Invariants

1. **Server-only derivation invariant**
   - Client must never provide required signers/departments for a release.

2. **Immutable signature invariant**
   - Signature records are append-only; no edits/deletes.

3. **Obligation-context invariant (ADR-019 pattern)**
   - Every signature must validate existence + ownership + obligation context.

4. **Content binding invariant**
   - Signature must bind to `signedCommitShas` for release changes at signing time.

5. **Snapshot consistency invariant**
   - `obligationsInputHash` must match release changed docs/commit set at submit time.

6. **Atomic satisfy invariant**
   - Signature creation and obligation satisfaction update must be transactional/atomic.

7. **Audit completeness invariant**
   - Audit entries required for: derivation completed, release submitted, signature accepted, auto-approval transition, publish.

8. **No hidden override invariant**
   - No manual bypass of pending obligations; any administrative override must be explicit, audited, and policy-approved.

---

## 5) Suggested Implementation Order + Dependency Graph

## Phase 0 — ADR and contract decisions (required first)

- Draft ADR: “Release signing obligations derived from document frontmatter.”
- Update ADR-005 / ADR-007 references to remove `requiredDepartments` authority.

## Phase 1 — Document signing source normalization

- Update parser/sync to persist normalized `author/reviewers/approvers` fields on documents.
- Enforce frontmatter schema (hard cut).

## Phase 2 — Data models and repositories

- Update `ReleaseModel` and release signature model.
- Add repository methods for obligation lifecycle and atomic satisfaction.

## Phase 3 — Contracts + API routes

- Apply breaking contract changes (`requiredDepartments` removed, `obligationId` introduced).
- Update route validators and response mappers.

## Phase 4 — Service workflow migration

- Implement derivation engine.
- Replace department-based eligibility with obligation-based eligibility.
- Update submit/approve logic.

## Phase 5 — UI adaptation

- Remove required department input from release creation/edit.
- Display obligation list/status and sign-by-obligation action.

## Phase 6 — Hard cut rollout controls

- Block deployment if any non-published release still relies on legacy fields.
- Apply hard-cut reset policy for incompatible draft/in-review releases instead of migration.

### Dependency graph

```text
ADR decision
  -> document frontmatter normalization
    -> release/signature data model changes
      -> contracts/routes changes
        -> service derivation + validation
          -> UI changes
            -> hard-cut rollout
```

---

## Final recommendation

Proceed with a **single-obligation-snapshot-per-release** model and **obligation-bound signatures**. This cleanly removes `requiredDepartments` drift, aligns with ADR-018 server-trust principles, and strengthens ADR-019-style entity reference validation for Part 11 defensibility.
