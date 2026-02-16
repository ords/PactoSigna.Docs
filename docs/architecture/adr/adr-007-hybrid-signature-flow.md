# ADR-007: Hybrid Signature Flow

## Status

Accepted

## Date

2026-01-13

## Context

Quality Controlled Change Releases require Part 11 compliant signatures from multiple stakeholders. We need to define the signature collection workflow.

Options considered:

| Option     | Flow                                          | Characteristics                        |
| ---------- | --------------------------------------------- | -------------------------------------- |
| Parallel   | All reviewers sign simultaneously             | Fast, but no sequential accountability |
| Sequential | Fixed order: Author → Reviewers → Approver    | Slow, clear chain                      |
| Hybrid     | Dept reviewers parallel → Final approver last | Balanced                               |

21 CFR Part 11 requirements:

- Signatures must be attributable to a specific individual
- Signature meaning must be clear (author, reviewer, approver)
- Signatures must be bound to the signed content (commit SHA)
- Timestamp must be recorded

## Decision

**Implement hybrid signature flow: parallel department review, sequential final approval.**

### Signature Flow

```
Release Created (Draft)
         │
         ▼
Release Submitted for Review
         │
         ├──► Engineering Dept ──┐
         │    (any member)       │
         │                       │
         ├──► Quality Dept ──────┼──► All required depts signed?
         │    (any member)       │              │
         │                       │              │
         ├──► Security Dept ─────┘              │
         │    (any member)                      │
         │                                      ▼
         │                              Final Approver
         │                              (Admin + specific dept)
         │                                      │
         │                                      ▼
         └──────────────────────────────► Published
```

### Signature Types and Meanings

| Type             | Who                  | Meaning                                      |
| ---------------- | -------------------- | -------------------------------------------- |
| `author`         | PR creator           | "I authored these changes"                   |
| `dept_reviewer`  | Department member    | "I have reviewed on behalf of [Department]"  |
| `final_approver` | Admin (typically QA) | "I approve this release for publication"     |
| `trainee`        | Staff member         | "I have read and understood this document"   |
| `auditor`        | External auditor     | "I acknowledge receipt of this audit report" |

### Data Model

```typescript
// Release signature tracking
/organizations/{orgId}/releases/{releaseId}
{
  status: 'draft' | 'in_review' | 'approved' | 'published'

  // Required signatures based on document types in release
  requiredDepartments: string[]

  // Collected signatures
  signatures: SignatureReference[]

  // State timestamps
  submittedForReviewAt?: Timestamp
  approvedAt?: Timestamp
  publishedAt?: Timestamp
}

// Individual signatures (immutable)
/organizations/{orgId}/signatures/{signatureId}
{
  releaseId: string
  userId: string
  userEmail: string           // Denormalized for audit
  userDepartment: string      // Denormalized for audit

  signatureType: 'author' | 'dept_reviewer' | 'final_approver'
  meaning: string             // "I have reviewed on behalf of Engineering"

  // What was signed
  documentIds: string[]       // Documents in release at time of signature
  commitShas: string[]        // Exact versions signed

  // Part 11 fields
  timestamp: Timestamp        // Server-side timestamp
  ipAddress?: string          // Optional for enhanced audit
}
```

### Signature Flow State Machine

```typescript
type ReleaseStatus = 'draft' | 'in_review' | 'approved' | 'published';

const transitions: Record<ReleaseStatus, ReleaseStatus[]> = {
  draft: ['in_review'], // Submit for review
  in_review: ['draft', 'approved'], // Recall or all signed
  approved: ['published'], // Publish
  published: [], // Terminal state
};

function canTransition(release: Release, targetStatus: ReleaseStatus): boolean {
  if (!transitions[release.status].includes(targetStatus)) return false;

  if (targetStatus === 'approved') {
    // All required departments must have signed
    return release.requiredDepartments.every(dept =>
      release.signatures.some(s => s.signatureType === 'dept_reviewer' && s.userDepartment === dept)
    );
  }

  if (targetStatus === 'published') {
    // Must have final approver signature
    return release.signatures.some(s => s.signatureType === 'final_approver');
  }

  return true;
}
```

## Consequences

### Positive

- **Efficient**: Parallel department review saves time
- **Controlled**: Final approver is gatekeeper
- **Flexible**: Department requirements configurable per org
- **Auditable**: Every signature is immutable record
- **Part 11 compliant**: Meaning, timestamp, binding all captured

### Negative

- **Complexity**: State machine has multiple paths
- **Notification burden**: Must notify all eligible reviewers
- **Signature conflicts**: What if dept member signs then leaves org?

### Edge Cases

| Scenario                          | Handling                                |
| --------------------------------- | --------------------------------------- |
| User leaves dept after signing    | Signature remains valid (point-in-time) |
| Release recalled after signatures | Signatures invalidated, must re-sign    |
| Same user in multiple depts       | Can sign once per department            |
| No one in required dept           | Admin can override or add user to dept  |
