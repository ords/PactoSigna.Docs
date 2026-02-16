# Release Signing Hard-Cut Migration — Immediate Execution Breakdown

Date: 2026-02-14  
Status: Execution-ready (Attempt 1 of 3)  
Scope: Release signing hard cut only (no backward compatibility)

Inputs consolidated from:

- `docs/plans/2026-02-14-release-signing-hard-cut-execution-plan.md`
- `docs/plans/2026-02-14-release-signing-obligations-migration-architecture.md`
- `docs/plans/2026-02-14-release-signing-model-hard-cut-product-review.md`
- `docs/plans/2026-02-14-derived-release-signer-obligations-quality-gate.md`

## Execution assumptions (single squad)

- Roles available each day: Architect, Backend, Frontend, Quality.
- Hard cut before launch: legacy release fields/flows are removed, not migrated.
- Parallelization is used where dependencies allow, but critical path is protected.
- Work includes mandatory items: ADR updates, contract cut, sync normalization, service rewrite, UI removal of manual departments, and test gates.

## Decision Lock

1. Owner membership gate: submit-for-review is blocked if any changed document owner email is not an active organization member.
2. Approver cardinality: each changed document must declare exactly one approver department.
3. Obligation dedupe: obligations merge by `(role, principal)` across changed documents and retain source evidence (`documentIds`, `commitShas`).
4. Recall/resubmit behavior: resubmit regenerates obligations from the latest changed-document snapshot.
5. No published baseline rule: when no prior published release exists, all currently synced in-scope documents are treated as changed.

---

## 1) Two-week kickoff plan

## Week 1 (Architecture lock + backend cut foundations)

- **Day 1 (Mon):**
  - Task 1 ADR lock starts and completes.
  - Task 2 contract cut design starts after ADR decisions are explicit.
- **Day 2 (Tue):**
  - Task 2 contract schemas implemented.
  - Task 3 sync normalization starts in parallel.
- **Day 3 (Wed):**
  - Task 3 sync normalization completed with parser/sync tests.
  - Task 4 data/repository model cut starts.
- **Day 4 (Thu):**
  - Task 4 repository and index changes completed.
  - Task 5 service derivation + submit/review rewrite starts.
- **Day 5 (Fri):**
  - Task 5 core service rewrite continues.
  - Task 9 quality gate test design begins in parallel (test matrix + abuse/race coverage definitions).

Week 1 outcome target:

- Legacy contract removed.
- Normalized signer metadata available from sync.
- Release model/repository supports obligation snapshot lifecycle.
- Service rewrite at feature-complete code level, entering hard validation.

## Week 2 (workflow completion + frontend hard cut + gate close)

- **Day 6 (Mon):**
  - Task 5 service rewrite complete including signature fulfillment + auto-approve atomic path.
  - Task 6 API route + mapper alignment complete.
- **Day 7 (Tue):**
  - Task 7 frontend hard cut starts (remove manual departments).
  - Task 8 signature UX switches to obligationId flow.
- **Day 8 (Wed):**
  - Tasks 7 and 8 complete with frontend tests.
  - Task 10 hard data reset + re-sync starts in dev/staging.
- **Day 9 (Thu):**
  - Task 9 executes quality gates (unit/integration/contract/concurrency/security abuse tests).
  - Fix-only window for release-signing defects.
- **Day 10 (Fri):**
  - Final verification pass for go/no-go.
  - Close unresolved blockers or produce explicit hold report.

Week 2 outcome target:

- End-to-end obligation-driven lifecycle live in dev/staging.
- UI has zero manual signer-policy controls.
- Quality/security/concurrency gates pass.
- Hard-cut readiness decision is evidence-based.

---

## 2) Assignable tasks (10)

## Task 1 — ADR hard-cut lock

- **Owner role:** Architect
- **Objective:** Update ADR-005/ADR-007 to supersede `requiredDepartments` authority with obligation model and frontmatter signer semantics.
- **Entry criteria:** Product invariants agreed from 2026-02-14 review docs.
- **Exit criteria:**
  - ADR text explicitly states server-only derivation and no client signer policy authority.
  - Missing/malformed signer metadata failure policy is defined.
  - No remaining ADR language describes runtime authority for `requiredDepartments`.

## Task 2 — Breaking contract cut (requests/responses/signature request)

- **Owner role:** Backend
- **Objective:** Remove legacy fields and enforce obligation-based DTOs (`obligationId`, `signingObligations`, derivation metadata).
- **Entry criteria:** Task 1 decisions merged or approved for implementation.
- **Exit criteria:**
  - Create/update release requests reject `requiredDepartments`.
  - Add-signature contract requires `obligationId` and no free-form authority path.
  - Contract package compiles and downstream services consume new schemas.

## Task 3 — Sync normalization of signer metadata

- **Owner role:** Backend
- **Objective:** Normalize and persist `author/reviewers/approvers` as first-class document fields in parser/sync.
- **Entry criteria:** Signer metadata shape locked by Task 1.
- **Exit criteria:**
  - Parsed documents persist normalized signer fields (not metadata-only).
  - Invalid signer metadata fails sync deterministically.
  - Parser/sync tests cover valid, missing, malformed metadata.

## Task 4 — Release/data model + repository obligation lifecycle

- **Owner role:** Backend
- **Objective:** Replace release `requiredDepartments` storage with `signingObligations` snapshot and fulfillment operations.
- **Entry criteria:** Task 2 DTO direction and Task 3 normalized source fields available.
- **Exit criteria:**
  - Repository supports derive/store/fetch/fulfill obligation lifecycle.
  - Any new composite queries have indexes in `firebase/firestore.indexes.json`.
  - `firestore-indexes.test.ts` has `hasIndex(...)` coverage for new query shapes.

## Task 5 — Service rewrite: derivation + workflow enforcement

- **Owner role:** Backend
- **Objective:** Rewrite release workflow to obligation-driven semantics (derive, snapshot on submit, validate/fulfill, auto-approve).
- **Entry criteria:** Tasks 2–4 complete.
- **Exit criteria:**
  - Submit-for-review validates obligation snapshot, not department list.
  - Signature validation enforces existence/ownership/context per obligation.
  - Signature write + obligation fulfillment + auto-approval transition are atomic and race-safe.
  - No code path uses release-level `requiredDepartments` for approval logic.

## Task 6 — Route/controller/mapper alignment for hard cut

- **Owner role:** Backend
- **Objective:** Align mutation/query routes and mappers to new contracts and models.
- **Entry criteria:** Task 5 service behavior stable in tests.
- **Exit criteria:**
  - All release routes serialize/validate obligation-based payloads.
  - Legacy contract regression tests confirm old shapes are rejected.
  - Controller/service tests updated and passing for create→submit→sign→approve lifecycle.

## Task 7 — Frontend hard cut: remove manual departments UX

- **Owner role:** Frontend
- **Objective:** Remove required-departments controls and any API payload usage from release creation/edit surfaces.
- **Entry criteria:** Task 2 contract cut merged.
- **Exit criteria:**
  - Manual department controls removed from release UI.
  - Create/update release API calls send no signer-policy fields.
  - Frontend tests updated for removed controls.

## Task 8 — Frontend sign flow migration to obligation binding

- **Owner role:** Frontend
- **Objective:** Display derived obligations and submit signatures by `obligationId`.
- **Entry criteria:** Tasks 5–6 API behavior available in env.
- **Exit criteria:**
  - Release detail shows obligation list + pending/satisfied state.
  - Signature action submits `obligationId` (no free-form role selection authority).
  - API/service tests for signatures updated and passing.

## Task 9 — Quality gates execution (compliance/security/performance)

- **Owner role:** Quality
- **Objective:** Execute mandatory gate matrix from quality review and block merge on failures.
- **Entry criteria:** Tasks 5, 6, 8 complete in integration environment.
- **Exit criteria:**
  - Concurrency tests prove one-success semantics for double-sign race.
  - Abuse cases (forged role, replay, cross-release misuse, owner mismatch) pass expected deny behavior.
  - Audit event coverage exists for derive, submit, fulfill, auto-approve, publish.
  - Performance checks meet agreed thresholds or approved exceptions documented.

## Task 10 — Hard data cutover + stabilization

- **Owner role:** Quality
- **Objective:** Enforce pre-launch hard cut by resetting incompatible non-published data and re-syncing normalized documents.
- **Entry criteria:** Tasks 3, 8, 9 passing in staging-like environment.
- **Exit criteria:**
  - Non-published legacy releases purged/recreated per hard-cut policy.
  - Repository sync rerun confirms normalized signer fields present.
  - Final smoke journey passes on clean hard-cut data set.

Cross-task policy enforcement notes:

- Task 5 must enforce owner active-membership and exactly-one-approver validation at submit-for-review.
- Task 5 derivation must dedupe by `(role, principal)` while preserving source evidence lists.
- Task 5 recall/resubmit must regenerate obligations from the latest changed-document snapshot.
- Task 5 baseline logic must treat all currently synced in-scope documents as changed when no prior published release exists.

---

## 3) Dependency order and critical path

## Dependency order (high level)

1. Task 1 (ADR lock)
2. Task 2 (contract cut) + Task 3 (sync normalization) in parallel after Task 1
3. Task 4 (data/repo model) after Tasks 2 and 3
4. Task 5 (service rewrite) after Task 4
5. Task 6 (route/mapper alignment) after Task 5
6. Task 7 (UI manual control removal) after Task 2
7. Task 8 (UI obligation sign flow) after Tasks 6 and 7
8. Task 9 (quality gates) after Tasks 5, 6, and 8
9. Task 10 (hard data cutover) after Tasks 3, 8, and 9

## Critical path

Task 1 → Task 2 → Task 4 → Task 5 → Task 6 → Task 8 → Task 9 → Task 10

Notes on parallelization:

- Task 3 can run in parallel with Task 2 and finishes before Task 4.
- Task 7 can run in parallel with backend service completion once Task 2 is merged.
- Quality preparation (test case authoring) can start during Task 5 but gate execution waits for Task 8.

---

## 4) Objective entry/exit criteria per task

All entry/exit criteria are embedded in each task above and are objective/pass-fail.

Global gate to finish kickoff:

- All task exit criteria met for Tasks 1–10.
- No unresolved Sev-1/Sev-2 defects in release-signing hard-cut scope.
- No legacy compatibility behavior remaining in contracts, services, or UI.

---

## 5) Recommended GitHub issue titles and labels

Recommended label set vocabulary:

- `release-signing`, `hard-cut`, `pre-launch`, `no-backward-compat`
- role labels: `architect`, `backend`, `frontend`, `quality`
- gate labels: `compliance`, `security`, `performance`, `test-gate`
- workflow labels: `critical-path`, `parallelizable`, `blocking`

Issue recommendations:

1. **ADR: Hard-cut release signing obligations supersede requiredDepartments authority**  
   Labels: `release-signing`, `hard-cut`, `architect`, `critical-path`, `blocking`

2. **Contracts: Remove requiredDepartments and enforce obligationId-based signing DTOs**  
   Labels: `release-signing`, `hard-cut`, `backend`, `critical-path`, `blocking`

3. **Sync: Normalize document author/reviewers/approvers and fail invalid metadata**  
   Labels: `release-signing`, `backend`, `parallelizable`, `blocking`

4. **Data layer: Replace release requiredDepartments with signingObligations snapshot model**  
   Labels: `release-signing`, `backend`, `critical-path`, `blocking`

5. **Services: Rewrite release workflow to obligation-derived submit/sign/auto-approve logic**  
   Labels: `release-signing`, `backend`, `critical-path`, `blocking`, `compliance`

6. **API routes/mappers: Align release endpoints to hard-cut obligation contracts**  
   Labels: `release-signing`, `backend`, `critical-path`, `blocking`

7. **Frontend: Remove manual required-departments controls from release creation flow**  
   Labels: `release-signing`, `frontend`, `parallelizable`, `no-backward-compat`

8. **Frontend: Migrate signature UX to obligation selection and fulfillment state display**  
   Labels: `release-signing`, `frontend`, `critical-path`

9. **Quality gate: Validate compliance/security/concurrency/performance for obligation model**  
   Labels: `release-signing`, `quality`, `test-gate`, `compliance`, `security`, `performance`, `critical-path`, `blocking`

10. **Cutover: Hard reset non-published legacy releases and re-sync normalized signer metadata**  
    Labels: `release-signing`, `quality`, `pre-launch`, `hard-cut`, `critical-path`, `blocking`

---

## Failure Protocol

Policy decisions are locked. If conflicting text or behavior appears during execution, treat it as a defect:

1. Mark the affected issue `blocking`.
2. Reference this document’s `Decision Lock` section in the issue.
3. Correct implementation/docs immediately before merging affected work.
