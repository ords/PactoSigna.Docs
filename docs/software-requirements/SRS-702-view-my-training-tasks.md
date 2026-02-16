---
id: SRS-702
title: 'View My Training Tasks'
status: draft
links:
  - type: derives-from
    target: PRS-016
---

# View My Training Tasks

## Requirement

> **SRS-702.1:** The system **SHALL** display a training page listing all training tasks assigned to the current user.
>
> **SRS-702.2:** The system **SHALL** display for each task: document title, training type, due date, and status.
>
> **SRS-702.3:** The system **SHALL** support filtering by status (All, Pending, In Progress, Completed) and by document ID.
>
> **SRS-702.4:** The system **SHALL** sort tasks with pending and overdue tasks first.

## Context

The "My Training" page is each member's personal view of assigned training. The endpoint `GET /api/v2/organizations/:orgId/training/my-tasks` returns tasks filtered by the current user. Status filtering helps users focus on incomplete work.

## Classification

| Field           | Value               |
| --------------- | ------------------- |
| Safety Class    | A                   |
| Category        | Functional          |
| Bounded Context | Training Compliance |

## Detailed Specification

### Inputs

- Current user ID (from authentication)
- Optional filters: status, documentId

### Processing

1. Query training tasks where `memberId` matches the current user.
2. Apply optional status and document filters.
3. Sort with pending/overdue first.

### Outputs

- List of training tasks with metadata
- Filter controls

### Error Handling

- No tasks found: display "No training tasks assigned"

## Acceptance Criteria

1. **Given** a member with assigned training tasks, **when** they view the training page, **then** all their tasks are listed with document title, type, due date, and status.
2. **Given** filtering by status "Pending", **when** the filter is applied, **then** only pending tasks are shown.
3. **Given** overdue and pending tasks, **when** viewing the list, **then** overdue tasks appear before other statuses.
4. **Given** a member with no tasks, **when** viewing the training page, **then** a "No training tasks assigned" message is displayed.

## Traceability

| Direction | Link Type    | Target ID | Title                    |
| --------- | ------------ | --------- | ------------------------ |
| Up        | derives-from | PRS-016    | Training task assignment |

## Source

**STORY-702 — View My Training Tasks**

> **As a** Team Member **I want** to see a list of training tasks assigned to me **So that** I know what training I need to complete.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
