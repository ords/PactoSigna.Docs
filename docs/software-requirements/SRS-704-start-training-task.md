---
id: SRS-704
title: 'Start Training Task'
status: draft
links:
  - type: derives-from
    target: PRS-017
---

# Start Training Task

## Requirement

> **SRS-704.1:** The system **SHALL** allow the assigned member to start a training task that is in "pending" status, changing it to "in_progress".
>
> **SRS-704.2:** The system **SHALL** record a server-side `startedAt` timestamp.
>
> **SRS-704.3:** The system **SHALL** create an audit log entry with action `training_task.started`.

## Context

Starting a training task records when the member began their training. Only the assigned member can start their own task. The endpoint `POST /api/v2/organizations/:orgId/training/:taskId/start` handles the transition.

## Classification

| Field           | Value               |
| --------------- | ------------------- |
| Safety Class    | A                   |
| Category        | Functional          |
| Bounded Context | Training Compliance |

## Detailed Specification

### Inputs

- Training task ID
- Current user ID (must be assigned member)

### Processing

1. Validate task is in "pending" status.
2. Validate current user is the assigned member.
3. Update task status to "in_progress".
4. Record `startedAt` timestamp (server-side).
5. Create audit log entry.

### Outputs

- Task status changed to "in_progress"
- `startedAt` timestamp recorded
- Audit log entry

### Error Handling

- Task not in "pending" status: reject with "Task can only be started when pending"
- Not the assigned member: reject with 403 Forbidden

## Acceptance Criteria

1. **Given** a pending training task, **when** the assigned member starts it, **then** the status changes to "in_progress" with a server-side `startedAt` timestamp.
2. **Given** a task already in progress, **when** a start is attempted, **then** it is rejected.
3. **Given** a member trying to start another member's task, **when** the request is made, **then** it is rejected with 403.
4. **Given** a successful start, **when** checking the audit log, **then** `training_task.started` is recorded.

## Traceability

| Direction | Link Type    | Target ID | Title                        |
| --------- | ------------ | --------- | ---------------------------- |
| Up        | derives-from | PRS-017    | Training completion workflow |

## Source

**STORY-704 — Start Training Task**

> **As a** Team Member **I want** to start a training task **So that** the system records when I began training.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
