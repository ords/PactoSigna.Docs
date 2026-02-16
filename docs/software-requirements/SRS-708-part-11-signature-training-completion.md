---
id: SRS-708
title: 'Part 11 Signature on Training Completion'
status: draft
links:
  - type: derives-from
    target: PRS-018
---

# Part 11 Signature on Training Completion

## Requirement

> **SRS-708.1:** The system **SHALL** require a Part 11 compliant electronic signature (re-authentication, meaning capture) with `signatureType: 'trainee'` when completing quiz or acknowledge training.
>
> **SRS-708.2:** The system **SHALL** validate the provided `signatureId` using three-tier validation (ADR-019): existence (signature must exist in the database), ownership (signature must belong to the requesting user), and context (signature must reference this training task with `resourceType: 'training_task'` and `resourceId: taskId`).
>
> **SRS-708.3:** The system **SHALL** change the task status to "completed" with a server-side `completedAt` timestamp upon valid signature acceptance.
>
> **SRS-708.4:** The system **SHALL** create an audit log entry with action `training_task.completed`.

## Context

Part 11 electronic signatures on training completion make the record legally binding and audit-ready. The signature is created separately (via the standard signature flow) and its ID is provided to the completion endpoint `POST .../training/:taskId/complete` with body `{ signatureId }`. The server performs three-tier validation per ADR-019 to prevent signature forgery.

## Classification

| Field           | Value               |
| --------------- | ------------------- |
| Safety Class    | B                   |
| Category        | Security            |
| Bounded Context | Training Compliance |

## Detailed Specification

### Inputs

- Training task ID
- Signature ID (from a previously created Part 11 signature)

### Processing

1. Load signature by ID from Firestore.
2. Validate existence: signature must exist.
3. Validate ownership: `signature.userId === currentUserId`.
4. Validate context: `signature.resourceType === 'training_task'` and `signature.resourceId === taskId`.
5. Update task status to "completed".
6. Record `completedAt` timestamp (server-side).
7. Store `signatureId` on the task.
8. Create audit log entry.

### Outputs

- Task status changed to "completed"
- `completedAt` and `signatureId` recorded
- Audit log entry

### Error Handling

- Signature not found: reject with "Signature not found"
- Signature ownership mismatch: reject with 403 Forbidden
- Signature context mismatch: reject with 403 Forbidden
- Task not in correct state: reject with status error

## Acceptance Criteria

1. **Given** a member with a valid Part 11 signature, **when** they complete a training task, **then** the task is marked completed with a server-side timestamp.
2. **Given** a signature that doesn't exist, **when** completion is attempted, **then** it is rejected with "Signature not found".
3. **Given** a signature belonging to a different user, **when** completion is attempted, **then** it is rejected with 403.
4. **Given** a signature for a different resource, **when** completion is attempted, **then** it is rejected with 403.
5. **Given** successful completion, **when** checking the audit log, **then** `training_task.completed` is recorded.

## Traceability

| Direction | Link Type    | Target ID | Title                         |
| --------- | ------------ | --------- | ----------------------------- |
| Up        | derives-from | PRS-018    | Training compliance reporting |

## Source

**STORY-708 — Part 11 Signature on Training Completion**

> **As a** Team Member **I want** to provide a Part 11 compliant electronic signature when completing training **So that** my training completion is legally binding and audit-ready.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
