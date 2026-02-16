# ADR-019: Entity Reference Validation for Part 11 Compliance

## Status

Accepted

## Date

2026-02-04

## Context

PactoSigna is a QMS system requiring FDA 21 CFR Part 11 compliance, where electronic signatures are legally binding. A security review identified that the Training module accepted `signatureId` parameters without validation, allowing:

1. **Non-existent signatures**: Fabricated IDs accepted as valid
2. **Stolen signatures**: Using another user's signature ID
3. **Misappropriated signatures**: Reusing signatures from different contexts

For Part 11 compliance, an electronic signature must:

- Be unique to the individual
- Be verifiable as authentic
- Be linked to the specific record being signed

Accepting unvalidated entity references undermines all three requirements.

## Decision

**When accepting entity references (IDs) in API requests, always validate existence, ownership, and context-appropriateness before use.**

### The Three Validation Requirements

| Validation    | Question                                        | Failure Mode           |
| ------------- | ----------------------------------------------- | ---------------------- |
| **Existence** | Does this entity exist?                         | Fabricated/invalid IDs |
| **Ownership** | Does this entity belong to the requesting user? | Impersonation/fraud    |
| **Context**   | Is this entity appropriate for this operation?  | Misuse/replay attacks  |

### Implementation Pattern

```typescript
async completeTraining(
  userId: string,
  orgId: string,
  taskId: string,
  signatureId: string
): Promise<void> {
  // 1. EXISTENCE: Verify signature exists
  const signature = await this.signaturesRepo.findById(orgId, signatureId);
  if (!signature) {
    throw new DomainError(ErrorCode.NOT_FOUND, 'Signature not found');
  }

  // 2. OWNERSHIP: Verify signature belongs to this user
  if (signature.userId !== userId) {
    throw new DomainError(ErrorCode.FORBIDDEN, 'Signature does not belong to user');
  }

  // 3. CONTEXT: Verify signature was created for this task
  if (signature.resourceType !== 'training_task' || signature.resourceId !== taskId) {
    throw new DomainError(ErrorCode.FORBIDDEN, 'Signature not valid for this task');
  }

  // Now safe to use the signature
  await this.trainingTasksRepo.update(orgId, taskId, {
    status: 'completed',
    signatureId: signature.id,
    completedAt: Timestamp.now(),
  });
}
```

### Entities Requiring Validation

| Entity       | Ownership Field | Context Requirements                         |
| ------------ | --------------- | -------------------------------------------- |
| Signature    | `userId`        | `resourceType`, `resourceId` match operation |
| TrainingTask | `memberId`      | User is assigned member or admin             |
| QuizSession  | `userId`        | User owns the session                        |
| Document     | (org-scoped)    | User has read access                         |

## Consequences

### Positive

- **Fraud Prevention**: Cannot use fabricated or stolen entity references
- **Part 11 Compliance**: Signatures are verifiably linked to correct user and record
- **Audit Integrity**: All entity references are validated and logged
- **Defense in Depth**: Multiple validation layers protect against attacks

### Negative

- **Additional Queries**: Each validation requires database lookup
- **Repository Dependencies**: Services need access to validation repositories
- **Error Handling**: More specific error messages reveal entity existence (acceptable tradeoff)

### Neutral

- **Consistent Pattern**: Same validation pattern across all entity references
- **Test Complexity**: Tests must set up valid entity relationships

## Implementation Checklist

For signature validation in Training module:

- [ ] Inject `SignaturesRepository` into `TrainingService`
- [ ] Add signature validation in `completeTraining`:
  - [ ] Verify signature exists
  - [ ] Verify signature belongs to requesting user
  - [ ] Verify signature was for this training task
- [ ] Update tests to provide valid signatures

## Related Decisions

- [ADR-008: Backend-Only Data Access](adr-008-backend-only-firestore-writes.md) - Server-side validation
- [ADR-018: Server-Side Source of Truth](adr-018-server-side-compliance-data.md) - Never trust client data
