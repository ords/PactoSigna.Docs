---
id: SRS-401
title: 'Audit Log Service'
status: draft
links:
  - type: derives-from
    target: PRS-009
---

# Audit Log Service

## Requirement

> **SRS-401.1:** The system **SHALL** create an immutable audit log entry in Firestore (`/organizations/{orgId}/auditLog/{entryId}`) for every Cloud Function that mutates data.
>
> **SRS-401.2:** The system **SHALL** record in each audit entry: action, user ID, user email, resource type, resource ID, server-generated timestamp (`FieldValue.serverTimestamp()`), and before/after state details.
>
> **SRS-401.3:** The system **SHALL** prevent any client-side writes to the audit log collection via Firestore security rules (`allow write: if false`).
>
> **SRS-401.4:** The system **SHALL** log failed operations in the audit trail with error details.

## Context

The audit log service is a shared kernel used by all bounded contexts. It implements 21 CFR Part 11 requirements for immutable audit trails. The `AuditService.log(entry)` method is called by every Cloud Function performing mutations. Server-generated timestamps ensure tamper-proof timing. Firestore security rules fully prevent client writes.

## Classification

| Field           | Value    |
| --------------- | -------- |
| Safety Class    | B        |
| Category        | Security |
| Bounded Context | Audit    |

## Detailed Specification

### Inputs

- Organization ID
- Audit entry data: action, userId, userEmail, resourceType, resourceId, details (before/after/metadata)

### Processing

1. Construct audit entry with all required fields.
2. Add `FieldValue.serverTimestamp()` for the timestamp.
3. Write to `/organizations/{orgId}/auditLog/{entryId}`.
4. Firestore rules ensure no client writes or updates.

### Outputs

- Immutable audit log entry in Firestore
- Server-generated timestamp on the entry

### Error Handling

- Firestore write failure: log to Cloud Functions logs (secondary audit path), do not fail the parent operation
- Missing required fields: throw validation error

## Acceptance Criteria

1. **Given** any Cloud Function performing a data mutation, **when** the mutation executes, **then** an audit log entry is created with action, user, resource, and timestamp.
2. **Given** an audit log entry, **when** any client attempts to write, update, or delete it, **then** the operation is rejected by Firestore rules.
3. **Given** a failed operation, **when** the error occurs, **then** the failure is logged in the audit trail with error details.
4. **Given** an audit log entry, **when** examining the timestamp, **then** it is server-generated (not client-provided).

## Traceability

| Direction | Link Type    | Target ID | Title             |
| --------- | ------------ | --------- | ----------------- |
| Up        | derives-from | PRS-009    | Audit log capture |

## Source

**STORY-401 — Audit Log Service**

> **As the** PactoSigna system **I want** to log all mutations to an immutable audit trail **So that** we can demonstrate Part 11 compliance.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
