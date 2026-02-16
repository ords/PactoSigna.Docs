---
id: SRS-710
title: 'Admin Manual Task Creation'
status: draft
links:
  - type: derives-from
    target: PRS-018
---

# Admin Manual Task Creation

## Requirement

> **SRS-710.1:** The system **SHALL** allow admin users to manually create a training task for any active member, specifying: memberId, documentId, documentVersion, and trainingType.
>
> **SRS-710.2:** The system **SHALL** accept optional parameters: `dueDays` (defaults to organization setting) and `quizDefinition` (for quiz-type tasks, stored server-side per ADR-018).
>
> **SRS-710.3:** The system **SHALL** create an audit log entry for manual task creation.

## Context

Manual task creation supports ad-hoc training assignments outside the automated Change Control flow. Admins use the endpoint `POST .../training/tasks` with a `CreateTrainingTaskRequest` body. Quiz definitions are stored server-side to prevent answer leakage.

## Classification

| Field           | Value               |
| --------------- | ------------------- |
| Safety Class    | A                   |
| Category        | Functional          |
| Bounded Context | Training Compliance |

## Detailed Specification

### Inputs

- Member ID (must be an active member)
- Document ID (frontmatter document ID)
- Document version (commit SHA)
- Training type (quiz / acknowledge / instructor_led)
- Optional: dueDays, quizDefinition

### Processing

1. Validate admin authorization.
2. Validate member exists and is active.
3. Create training task with specified parameters.
4. Apply default due days from organization settings if not provided.
5. Store quiz definition server-side if provided.
6. Create audit log entry.

### Outputs

- New training task document in Firestore
- Audit log entry

### Error Handling

- Non-admin user: return 403
- Member not found or inactive: reject with "Member not found or inactive"
- Missing required fields: reject with validation error

## Acceptance Criteria

1. **Given** an admin, **when** they create a task for an active member with all required fields, **then** the task is created.
2. **Given** no `dueDays` specified, **when** the task is created, **then** the organization's default due days setting is used.
3. **Given** a quiz-type task with a quiz definition, **when** created, **then** the quiz definition is stored server-side.
4. **Given** a non-admin user, **when** manual task creation is attempted, **then** it is rejected with 403.

## Traceability

| Direction | Link Type    | Target ID | Title                         |
| --------- | ------------ | --------- | ----------------------------- |
| Up        | derives-from | PRS-018    | Training compliance reporting |

## Source

**STORY-710 — Admin Manual Task Creation**

> **As a** Quality Manager **I want** to manually create training tasks for specific members **So that** I can assign ad-hoc training outside the automated flow.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
