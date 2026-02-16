---
id: SRS-707
title: 'Instructor-Led Training with Batch Signoff'
status: draft
links:
  - type: derives-from
    target: PRS-017
---

# Instructor-Led Training with Batch Signoff

## Requirement

> **SRS-707.1:** The system **SHALL** allow an instructor (admin-permissioned user) to select multiple pending or in-progress training tasks and complete them in a single batch signoff.
>
> **SRS-707.2:** The system **SHALL** require the instructor to provide: evidence URL (valid URL), evidence hash (integrity verification), and meaning statement.
>
> **SRS-707.3:** The system **SHALL** complete all selected tasks with the instructor's signoff and create an audit log entry for each task.

## Context

Instructor-led training supports classroom and workshop scenarios where an instructor verifies competence in person. The batch signoff endpoint `POST .../training/batch-signoff` with body `{ taskIds, evidenceUrl, evidenceHash, meaning }` efficiently records completion for multiple members. Only admin-permissioned users can perform batch signoff.

## Classification

| Field           | Value               |
| --------------- | ------------------- |
| Safety Class    | B                   |
| Category        | Functional          |
| Bounded Context | Training Compliance |

## Detailed Specification

### Inputs

- Array of training task IDs
- Evidence URL (valid URL)
- Evidence hash (string)
- Meaning statement (text)

### Processing

1. Validate user has admin permissions.
2. Validate all task IDs exist and are in pending/in-progress status.
3. Validate evidence URL is a valid URL.
4. Complete each task with the instructor's evidence and meaning.
5. Create audit log entry for each task.

### Outputs

- All selected tasks completed
- Evidence URL and hash stored on each task
- Audit log entries for each task

### Error Handling

- Non-admin user: reject with 403
- Invalid task IDs: reject with specific errors per task
- Invalid evidence URL: reject with "Evidence URL must be a valid URL"
- Tasks already completed: skip with warning

## Acceptance Criteria

1. **Given** an admin selecting 5 pending tasks, **when** batch signoff is submitted with valid evidence, **then** all 5 tasks are completed.
2. **Given** a non-admin user, **when** batch signoff is attempted, **then** it is rejected with 403.
3. **Given** an invalid evidence URL, **when** batch signoff is submitted, **then** the request is rejected.
4. **Given** successful batch signoff, **when** checking the audit log, **then** `training_task.batch_signoff` entries exist for each task.

## Traceability

| Direction | Link Type    | Target ID | Title                        |
| --------- | ------------ | --------- | ---------------------------- |
| Up        | derives-from | PRS-017    | Training completion workflow |

## Source

**STORY-707 — Instructor-Led Training with Batch Signoff**

> **As an** Instructor (Quality Manager or Department Lead) **I want** to sign off on multiple members' training at once after an instructor-led session **So that** classroom or workshop training is efficiently recorded.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
