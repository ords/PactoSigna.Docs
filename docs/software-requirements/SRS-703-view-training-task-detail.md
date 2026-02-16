---
id: SRS-703
title: 'View Training Task Detail'
status: draft
links:
  - type: derives-from
    target: PRS-016
---

# View Training Task Detail

## Requirement

> **SRS-703.1:** The system **SHALL** display training task details including: document title, document version, training type, status, due date, creation date, start date (if started), completion date and quiz score (if completed), and associated signature ID (if completed).
>
> **SRS-703.2:** The system **SHALL** validate task ownership: only the assigned member or an admin can view a task's details.

## Context

The task detail view provides complete information about a specific training assignment. The endpoint `GET /api/v2/organizations/:orgId/training/:taskId` enforces ownership validation to protect member privacy.

## Classification

| Field           | Value               |
| --------------- | ------------------- |
| Safety Class    | A                   |
| Category        | Functional          |
| Bounded Context | Training Compliance |

## Detailed Specification

### Inputs

- Training task ID
- Current user ID (for ownership validation)

### Processing

1. Load training task from Firestore.
2. Validate the current user is the assigned member or an admin.
3. Return task details.

### Outputs

- Task detail view with all metadata fields

### Error Handling

- Task not found: return 404
- Not authorized: return 403 Forbidden

## Acceptance Criteria

1. **Given** an assigned member, **when** they view their task detail, **then** all task metadata is displayed.
2. **Given** a completed quiz task, **when** viewing detail, **then** the completion date and quiz score are shown.
3. **Given** a member viewing another member's task, **when** not an admin, **then** access is denied with 403.
4. **Given** an admin, **when** viewing any member's task, **then** access is granted.

## Traceability

| Direction | Link Type    | Target ID | Title                    |
| --------- | ------------ | --------- | ------------------------ |
| Up        | derives-from | PRS-016    | Training task assignment |

## Source

**STORY-703 — View Training Task Detail**

> **As a** Team Member **I want** to view the details of a specific training task **So that** I can understand what is required and begin the training.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
