---
id: WI-006
title: 'Part 11 Compliance Checklist'
status: draft
links:
  - type: related-to
    target: POL-003
---

# Part 11 Compliance Checklist

When implementing compliance-critical features (training, signatures, releases, audit logs):

## Never Trust Client Data for Scoring/Validation

- Quiz definitions with correct answers → store server-side, never accept from client
- Passing thresholds → server-side configuration only
- Grading logic → calculated entirely server-side
- See [ADR-018](../../architecture/adr/adr-018-server-side-compliance-data.md)

## Validate All Entity References

When accepting entity IDs (signatureId, documentId, etc.), always verify:

1. **Existence**: Entity must exist in database
2. **Ownership**: Entity must belong to the requesting user
3. **Context**: Entity must be appropriate for this operation

```typescript
// Example: Signature validation
const signature = await signaturesRepo.findById(orgId, signatureId);
if (!signature) throw new DomainError(ErrorCode.NOT_FOUND);
if (signature.userId !== userId) throw new DomainError(ErrorCode.FORBIDDEN);
if (signature.resourceId !== taskId) throw new DomainError(ErrorCode.FORBIDDEN);
```

See [ADR-019](../../architecture/adr/adr-019-entity-reference-validation.md)

## Electronic Signatures

- Signatures are immutable once created
- Require meaning selection and authentication
- Must be linked to specific resource (type + id)
- Validate ownership before accepting signatureId

## Audit Logging

- All mutations must create audit log entries
- Audit log entries are immutable (append-only)
- Include: who, what, when, and the before/after state
