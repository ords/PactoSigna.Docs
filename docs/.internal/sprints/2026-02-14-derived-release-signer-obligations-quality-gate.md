# Quality Gate Review: Derived Release Signer Obligations (Compliance/Security/Performance)

**Date:** 2026-02-14  
**Mode:** quality-security-performance  
**Scope:** Migration to server-derived signer obligations for release approvals  
**Target Behavior:**

- Signer obligations derived from changed documents
- Owner signer matched by email; reviewer/approver derived from department rules
- Auto-approval only after all obligations are fulfilled
- No backward compatibility

---

## Gate Decision (Attempt 1 of 3)

**Status: FAIL (pre-implementation gate)**

The approach is directionally correct for Part 11, but merge should be blocked until the implementation satisfies the mandatory checks in this document. Current release signing paths are still department/signature-type driven and allow client-submitted `signatureType`, which is incompatible with strict server-derived obligations.

---

## 1) Part 11 Compliance Checklist (Implementation Gate)

Use this as a merge checklist. Every item must be demonstrably true via tests and audit evidence.

### A. Signature integrity and attribution

- [ ] **Obligation snapshot is created server-side at release creation** and persisted immutably for the release lifecycle (`draft` onward).
- [ ] **Each obligation binds to concrete signing scope** (at least releaseId + commitShas/documentIds captured at derivation time).
- [ ] **Signer identity is resolved server-side** from authenticated principal; client cannot select signer identity.
- [ ] **Owner obligation uses email match against document metadata** (normalized comparison, case-insensitive), with deterministic behavior when owner is missing/invalid.
- [ ] **Reviewer/approver obligations derive from org reviewer rules** active at derivation time; later settings edits do not mutate existing obligations.

### B. Meaning, intent, and signature record completeness

- [ ] Signature capture requires explicit meaning text/selection and stores it with immutable signature record.
- [ ] Signature record includes signer userId, signer email, signer department, signature type/role, server timestamp.
- [ ] Signed record stores binding material (`signedCommitShas` and/or derived obligation document set).
- [ ] Signature record cannot be edited or reassigned after write.

### C. Validation (ADR-019 pattern: existence, ownership, context)

- [ ] On signing, server validates **obligation exists** and is pending.
- [ ] Server validates **ownership/eligibility** (user is exact owner-email target or valid department member for that obligation type).
- [ ] Server validates **context** (obligation belongs to release, release in valid status, obligation not already fulfilled).
- [ ] Replay attempts (same obligation signed twice, or reused signature reference) are rejected with deterministic error code.

### D. Workflow and state transitions

- [ ] Release transitions to `approved` **only** when all required obligations are fulfilled (not merely all required departments).
- [ ] Auto-approval is server-evaluated in the same transaction/critical section as signature write.
- [ ] Publish remains blocked unless release is `approved` and required approval obligations are fulfilled.

### E. Audit trail (Part 11 traceability)

- [ ] Audit event for obligation derivation at release creation includes derivation inputs summary (changed docs count, rule version/hash, derived obligations count).
- [ ] Audit event for every fulfilled obligation includes before/after state, signer identity, obligation id/type, release id.
- [ ] Audit event for auto-approval includes fulfilled-obligation summary and final evaluation result.
- [ ] All mutation paths (derive, fulfill, approve, publish, withdraw/recompute if allowed) are audit logged.

### F. No backward compatibility enforcement

- [ ] Legacy payload fields that let client drive policy (`signatureType`, direct `requiredDepartments` authority for approval) are removed from write path or ignored and rejected.
- [ ] Existing endpoints fail closed if legacy mode is attempted.
- [ ] API contracts reflect obligation-driven model only.

---

## 2) Security Checks / Abuse Cases to Test

### High-severity abuse cases

1. **Client-forged role/signature type**
   - Attempt to sign with `signatureType=final_approver` when user only satisfies reviewer obligation.
   - Expected: ignored or rejected; server uses obligation derivation only.

2. **Owner email spoof / mismatch**
   - User with different email tries owner obligation.
   - Email case variants (`Alice@x.com` vs `alice@x.com`) and whitespace normalization checks.
   - Expected: strict normalized match; no bypass.

3. **Cross-release obligation replay**
   - Reuse signature/obligation reference from Release A to fulfill Release B.
   - Expected: rejected by context validation.

4. **Concurrent double-sign race**
   - Two requests fulfill same obligation simultaneously.
   - Expected: exactly one succeeds; no duplicate fulfillment; idempotent failure for second.

5. **Premature approval race**
   - Parallel signing requests on final missing obligations.
   - Expected: one deterministic approval transition, consistent audit trail, no split-brain status.

6. **Rule tampering after derivation**
   - Change organization reviewer rules during in-review release.
   - Expected: existing obligation set unchanged unless explicit re-derive workflow is invoked and audit logged.

7. **Missing/invalid document owner metadata**
   - Changed document missing owner email, malformed owner, or owner outside organization.
   - Expected: fail-safe behavior defined (block submission/approval with explicit error) and auditable.

8. **Department privilege escalation**
   - User manually changes department client-side or sends forged department claim.
   - Expected: server uses persisted member department only.

### Medium-severity abuse cases

9. **Denial of approval via obligation inflation**
   - Adversarial metadata creates duplicate obligations for same signer.
   - Expected: deterministic dedupe strategy.

10. **Information leakage**
    - Error messages reveal who is pending signer or sensitive org structure to unauthorized user.
    - Expected: least-privilege error responses.

11. **Audit suppression attempt**
    - Simulate repo failure where write succeeds but audit fails (or vice versa).
    - Expected: atomicity or compensating failure policy; no silent unsigned mutations.

---

## 3) Performance Considerations

## Release creation (derivation path)

- Derivation complexity should be roughly `O(changedDocs + rules)` after pre-indexing rules by `documentType`.
- Avoid N+1 document reads during derivation:
  - Prefer deriving from already-fetched changed document payload when possible.
  - If extra lookups are required, use batched reads and bounded concurrency.
- Materialize and store obligations once; signing flow should not recompute full derivation.
- Add Firestore indexes for any new obligation queries and lock with `firestore-indexes.test.ts` assertions.

## Signing path

- Fulfill obligation and evaluate auto-approval in one transactional boundary.
- Query only pending obligations needed for this release/signature check; avoid scanning full signatures collection.
- Use idempotency key or uniqueness constraints (`releaseId + obligationId + signerId`) to prevent duplicate writes under retries.

## Operational guardrails (recommended SLO targets)

- P95 release creation (with derivation): <= 500ms at typical changed-doc volume.
- P95 signing request processing: <= 250ms single obligation fulfillment.
- Read/write amplification budget: bounded and linear with changed-doc count.
- Cloud Function cold-start: keep derivation logic modular and dependency-light; avoid heavyweight initialization in hot path.

## Data model/perf recommendations

- Store obligations as a dedicated subcollection (`releases/{releaseId}/obligations`) if cardinality can grow; this improves filtered reads and avoids large parent document rewrites.
- Track summary counters on release (`obligationsTotal`, `obligationsFulfilled`) for constant-time auto-approval checks.
- Keep immutable derivation metadata (`derivationVersion`, `rulesHash`, `derivedAt`) for diagnostics and cache invalidation decisions.

---

## 4) Must-Have Test Assertions Before Merge

These are blocking assertions (unit + integration, with targeted e2e where relevant).

### Derivation correctness

- [ ] Given changed docs with mixed types, derived obligations exactly match expected owner/reviewer/approver set.
- [ ] Owner obligations use normalized email matching; invalid/missing owner metadata follows fail-safe policy.
- [ ] Reviewer/approver obligations reflect organization reviewer rules snapshot at derivation time.
- [ ] Duplicate obligations across docs are deduped per defined policy.

### Validation and authorization

- [ ] Signing fails when no matching pending obligation exists for the caller.
- [ ] Signing fails for already fulfilled obligation.
- [ ] Signing fails for release not in `in_review`.
- [ ] Client-provided signature type/role has no authority (rejected or ignored with test proof).

### Auto-approval semantics

- [ ] Release remains `in_review` until all obligations fulfilled.
- [ ] Release transitions to `approved` immediately after last required obligation is fulfilled.
- [ ] Transition occurs once under concurrent signing.
- [ ] Approval does not depend solely on `requiredDepartments` legacy behavior.

### Audit and compliance evidence

- [ ] Audit entry exists for derivation event with obligation summary.
- [ ] Audit entry exists for each fulfillment with obligation identity.
- [ ] Audit entry exists for auto-approval transition with before/after.
- [ ] No mutation path bypasses audit creation.

### Migration/no-compatibility

- [ ] Legacy write contract tests fail (or are removed) for old signing mode.
- [ ] Contract tests enforce new obligation-based request/response schemas.
- [ ] Existing releases created pre-migration are either explicitly unsupported or handled by documented migration path; behavior is deterministic and tested.

### Firestore/index safety

- [ ] Every new composite query has matching index in `firebase/firestore.indexes.json`.
- [ ] `packages/data/src/repositories/firestore-indexes.test.ts` includes `hasIndex(...)` checks for new queries.

---

## Unresolved Risks (Must Be Closed)

1. **Current API accepts client `signatureType`** in release signing flow, which is incompatible with strict server-derived obligations.
2. **Current auto-approval logic is department-based** (`requiredDepartments`) and does not enforce obligation completion semantics.
3. **Potential race conditions** in concurrent signing/approval unless transactional uniqueness and atomic status transitions are implemented.
4. **Undefined fail-safe behavior** for missing/malformed owner email metadata could cause compliance gaps.

Until these risks are addressed and tested, this migration should not pass the quality gate.
