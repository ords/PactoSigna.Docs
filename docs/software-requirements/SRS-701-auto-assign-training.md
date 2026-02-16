---
id: SRS-701
title: 'Auto-Assign Training on Change Control Effectiveness'
status: draft
links:
  - type: derives-from
    target: PRS-016
---

# Auto-Assign Training on Change Control Effectiveness

## Requirement

> **SRS-701.1:** The system **SHALL** create training tasks when a Change Control becomes effective, checking each included document for training configuration in frontmatter metadata (`metadata.training`).
>
> **SRS-701.2:** The system **SHALL** create tasks for active members in the configured `required_departments`, with the training type determined by the document's `training_mode` (quiz, acknowledge, or instructor_led) and due date calculated from `due_days` configuration (default: 30 days).
>
> **SRS-701.3:** The system **SHALL** fetch quiz definitions from GitHub (e.g., `SOP-001-quiz.json`) and store them server-side in the training task for quiz-type training (ADR-018).
>
> **SRS-701.4:** The system **SHALL** prevent duplicate tasks: if a member already has a pending or in-progress task for the same document, no new task is created.
>
> **SRS-701.5:** The system **SHALL** create an audit log entry with action `training_task.bulk_created`.

## Context

Training auto-assignment is implemented via the `trainingAssignmentWorker` Cloud Function triggered by Cloud Tasks. When a Change Control becomes effective, the system determines which documents require training, queries active members in the required departments, and creates tasks. Quiz definitions are stored server-side per ADR-018 to prevent answer leakage to the client.

## Classification

| Field           | Value               |
| --------------- | ------------------- |
| Safety Class    | B                   |
| Category        | Functional          |
| Bounded Context | Training Compliance |

## Detailed Specification

### Inputs

- Change Control effectiveness event payload: `{ organizationId, documentId, commitSha, changeControlId }`
- Document frontmatter training configuration: `{ training_mode, required_departments, due_days }`
- Quiz definition file from GitHub (for quiz-type training)

### Processing

1. Receive Cloud Task with Change Control payload.
2. Read document frontmatter for training configuration.
3. Query active members in `required_departments`.
4. For each member, check for existing pending/in-progress tasks for same document.
5. Create training task with appropriate type, due date, and (for quizzes) server-side quiz definition.
6. Create audit log entry.

### Outputs

- Training task documents in Firestore
- Audit log entry for bulk creation

### Error Handling

- Document has no training configuration: skip silently
- Quiz definition file not found on GitHub: log error, skip quiz task creation
- Member query failure: log error, retry via Cloud Tasks

## Acceptance Criteria

1. **Given** a Change Control becoming effective with a document configured for training, **when** the worker processes it, **then** training tasks are created for all active members in the required departments.
2. **Given** a member with an existing pending task for the same document, **when** a new Change Control triggers, **then** no duplicate task is created.
3. **Given** a quiz-type training document, **when** the task is created, **then** the quiz definition is fetched from GitHub and stored server-side in the task.
4. **Given** a training configuration with `due_days: 14`, **when** tasks are created, **then** the due date is 14 days from creation.

## Traceability

| Direction | Link Type    | Target ID | Title                    |
| --------- | ------------ | --------- | ------------------------ |
| Up        | derives-from | PRS-016    | Training task assignment |

## Source

**STORY-701 — Auto-Assign Training on Change Control Effectiveness**

> **As a** System **I want** to create training tasks when a Change Control becomes effective **So that** affected members are automatically notified of required training.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
