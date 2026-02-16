---
id: SRS-709
title: 'Admin Training Dashboard'
status: draft
links:
  - type: derives-from
    target: PRS-018
---

# Admin Training Dashboard

## Requirement

> **SRS-709.1:** The system **SHALL** provide an admin-only training dashboard showing aggregate statistics: pending, in-progress, completed, and overdue counts, plus completion rate percentage.
>
> **SRS-709.2:** The system **SHALL** list all training tasks across all members (not just the current user's) with filtering by status, member, and document.
>
> **SRS-709.3:** The system **SHALL** clearly identify overdue tasks in the dashboard.

## Context

The admin training dashboard enables Quality Managers to monitor compliance across the organization. The endpoint `GET .../training/dashboard` with query params `status?`, `memberId?`, `documentId?` provides the data. Only admin users can access this view.

## Classification

| Field           | Value               |
| --------------- | ------------------- |
| Safety Class    | A                   |
| Category        | Functional          |
| Bounded Context | Training Compliance |

## Detailed Specification

### Inputs

- Organization ID
- Optional filters: status, memberId, documentId

### Processing

1. Validate admin authorization.
2. Query all training tasks for the organization.
3. Calculate aggregate statistics.
4. Apply optional filters.
5. Identify overdue tasks (past due date, not completed).

### Outputs

- Aggregate stats: pending, in-progress, completed, overdue counts, completion rate
- Filtered list of all training tasks
- Overdue task highlighting

### Error Handling

- Non-admin access: return 403 Forbidden
- No tasks found: display zero counts

## Acceptance Criteria

1. **Given** an admin, **when** they view the training dashboard, **then** aggregate statistics (pending, in-progress, completed, overdue, completion rate) are displayed.
2. **Given** an admin filtering by member, **when** the filter is applied, **then** only that member's tasks are shown.
3. **Given** overdue tasks, **when** viewing the dashboard, **then** overdue tasks are clearly highlighted.
4. **Given** a non-admin user, **when** they attempt to access the dashboard, **then** access is denied.

## Traceability

| Direction | Link Type    | Target ID | Title                         |
| --------- | ------------ | --------- | ----------------------------- |
| Up        | derives-from | PRS-018    | Training compliance reporting |

## Source

**STORY-709 — Admin Training Dashboard**

> **As a** Quality Manager **I want** to see training status across all team members **So that** I can monitor compliance and identify overdue training.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
