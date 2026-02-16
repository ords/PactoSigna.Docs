# Release Signing Model Migration (Hard Cut) — Product Review

Date: 2026-02-14
Mode: Product review and refinement (no code)
Decision baseline: **No backward compatibility; pre-launch hard schema cut**

## Scope Context

Current state:

- Release creation accepts `requiredDepartments` from UI.
- Changed documents are server-calculated.

Target state:

- Required release signers/roles are **derived only** from changed documents since last release.
- Derivation uses document signer metadata only:
  - owner email
  - reviewer departments
  - approver department

Out of scope for this review:

- Technical implementation details
- UI design decisions

## Decision Lock

1. Owner membership gate: submit-for-review is blocked if any changed document owner email is not an active organization member.
2. Approver cardinality: each changed document must declare exactly one approver department.
3. Obligation dedupe: obligations merge by `(role, principal)` across changed documents and retain source document evidence lists.
4. Recall/resubmit behavior: resubmit regenerates obligations from the latest changed-document snapshot.
5. No published baseline rule: when no prior published release exists, all currently synced in-scope documents are treated as changed.

---

## 1) Top 8 Product Requirements / Invariants to Lock

1. **Single Source of Truth for signing requirements**
   - The system SHALL derive required signers/roles exclusively from server-calculated changed documents + document signer metadata.
   - The client MUST NOT submit or override required departments/signers.

2. **Deterministic derivation**
   - For the same organization, release baseline, and changed document set, signer requirements SHALL be deterministic and reproducible.
   - Re-opening/re-fetching a draft release without document changes SHALL return identical required signer requirements.

3. **Hard schema cut: no legacy field acceptance**
   - Release create/update APIs SHALL reject payloads containing legacy `requiredDepartments` input fields.
   - Persisted release signing requirement shape SHALL only contain derived signer requirement structures.

4. **Document-delta boundedness**
   - Only documents changed since the last published release SHALL contribute signing requirements for a new release.
   - Unchanged documents MUST NOT introduce new required signers for that release.

5. **Role mapping completeness from metadata**
   - For each changed document, derived requirements SHALL include:
     - owner signer requirement from owner email
     - reviewer signer requirement(s) from reviewer departments
     - approver signer requirement from approver department
   - `approvers` metadata MUST contain exactly one department per changed document.
   - Missing required signer metadata on any changed document SHALL block submit-for-review with explicit remediation errors.

6. **Release snapshot immutability at review start**
   - At submit-for-review, signer requirements SHALL be snapshotted for that release instance.
   - Subsequent document metadata changes SHALL NOT retroactively alter an in-review release’s required signers.

7. **Approval gate correctness**
   - Release approval eligibility SHALL be computed solely against the snapshotted derived signer requirements.
   - A release SHALL be blocked from approval until all required derived signers/roles are satisfied according to policy.

8. **Auditability and explainability**
   - The system SHALL persist a machine-readable derivation summary per release (document IDs, metadata inputs, and resulting signer requirements).
   - Users SHALL be able to see why each signer requirement exists (traceable to changed document metadata).

---

## 2) MVP Scope Cut Recommendation (Now vs Later)

### Do now (MVP)

- Hard remove legacy `requiredDepartments` contract/input path.
- Derive owner/reviewer/approver requirements from changed document metadata only.
- Block submit-for-review when changed documents lack required signer metadata.
- Snapshot derived requirements at submit-for-review.
- Enforce approval gate against snapshot.
- Show basic derivation explainability in release detail (document-to-requirement trace).
- Add migration-safe pre-launch data handling rule: draft/in_review test data can be reset or recreated rather than transformed.

### Do later (post-MVP)

- Advanced delegation and signer substitution workflows.
- Multi-department membership nuances and “sign once for multiple departments” optimization.
- Conditional reviewer logic by risk class/document type beyond frontmatter metadata.
- Batch remediation workflows for missing metadata across many documents.
- Reporting dashboards (org-level signer requirement analytics).

### Scope cut rationale

- This preserves the hard-cut principle, keeps compliance-critical behavior deterministic, and avoids introducing policy complexity pre-launch.

---

## 3) Milestone Breakdown with Definition of Done

## Milestone 1 — Model Lock & Contract Cut

Goal:

- Finalize product semantics and remove legacy input path at requirements level.

Definition of Done:

- Product spec explicitly states “no client-provided required signer fields.”
- Legacy `requiredDepartments` marked removed/deprecated in requirements and API docs.
- Acceptance test matrix includes pass/fail examples for legacy payload rejection.
- Decision lock policy is reflected in acceptance tests for owner membership, approver cardinality, dedupe, recall/resubmit, and no-baseline behavior.

## Milestone 2 — Derivation Engine Product Acceptance

Goal:

- Validate signer requirement derivation behavior from changed documents + metadata.

Definition of Done:

- Acceptance scenarios exist for:
  - single changed document
  - multiple changed documents with overlapping reviewer departments
  - multiple owners
  - mixed owner/reviewer/approver requirements
- Determinism criteria pass across repeat runs.
- Missing metadata scenarios produce actionable blocking messages.
- Product sign-off confirms derived result format is understandable by QA and regulatory personas.

## Milestone 3 — Workflow Enforcement & Snapshot Integrity

Goal:

- Enforce lifecycle rules around submit, sign, and approve using derived snapshot.

Definition of Done:

- Submit-for-review creates immutable signer-requirement snapshot for that release state.
- Approval blocked until all required derived signers/roles are satisfied.
- Metadata changes after submit do not mutate in-review requirements.
- Recall/resubmit regenerates obligations from the latest changed-document snapshot and is acceptance-tested.

## Milestone 4 — Explainability, Audit Readiness, and UAT Exit

Goal:

- Ensure operational readiness and compliance traceability before launch.

Definition of Done:

- Each required signer can be traced to one or more changed documents and metadata fields.
- Audit events cover derivation, submit, signature capture, and approval gate decisions.
- UAT checklist executed by QA Manager + Regulatory Specialist personas with no Sev-1/Sev-2 gaps.
- Go/No-Go checklist approved for pre-launch release.

---

## 4) Top 5 Rollout Risks and Mitigations

1. **Risk: Missing/invalid signer metadata blocks many releases at once**
   - Mitigation:
     - Pre-cut metadata completeness audit on all active repositories.
     - Pre-launch remediation checklist per document owner.
     - Clear blocking error messages naming exact documents/fields.

2. **Risk: Unexpected signer explosion (too many required reviewers) from document overlap**
   - Mitigation:
     - Lock deduplication rules in product spec (e.g., department-level dedupe for reviewers).
     - Add scenario tests with high-overlap document sets.

3. **Risk: User confusion after hard cut due to removed manual controls**
   - Mitigation:
     - Explicit in-product messaging: “Signers are auto-derived from changed document metadata.”
     - Release detail explainability panel showing source documents and roles.

4. **Risk: Lifecycle inconsistency when documents change during review**
   - Mitigation:
     - Snapshot-on-submit invariant and recall/resubmit policy locked before UAT.
     - Test cases that prove no retroactive requirement drift.

5. **Risk: Compliance challenge if derivation decisions are not auditable**
   - Mitigation:
     - Persist derivation summary inputs/outputs per release.
     - Audit events for derivation execution and approval gate decisions.
     - UAT includes auditor-style trace verification scripts.

---

## Policy Gap Resolution Status

No unresolved migration policy gaps remain for hard-cut release signing in this review.

---

## Suggested Success Metrics (MVP)

- 100% of new releases created without client-provided signer requirement inputs.
- <5% of submit attempts blocked by missing metadata after remediation week.
- 0 approval events where required derived signers are incomplete.
- 100% of UAT sample releases provide end-to-end signer requirement traceability.
