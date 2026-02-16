---
id: SRS-711
title: 'Training Settings Management'
status: draft
links:
  - type: derives-from
    target: PRS-018
---

# Training Settings Management

## Requirement

> **SRS-711.1:** The system **SHALL** allow admin users to view and update organization-wide training settings: default due days (positive integer) and passing score percentage (0–100).
>
> **SRS-711.2:** The system **SHALL** apply updated settings to all newly created training tasks without retroactively changing existing tasks.
>
> **SRS-711.3:** The system **SHALL** create an audit log entry for any settings change.

## Context

Training settings provide organizational flexibility for training policies. The endpoints `GET .../training/settings` and `PATCH .../training/settings` with body `{ defaultDueDays?, passingScorePercent? }` manage settings. Only admin-permissioned users can update settings.

## Classification

| Field           | Value               |
| --------------- | ------------------- |
| Safety Class    | A                   |
| Category        | Functional          |
| Bounded Context | Training Compliance |

## Detailed Specification

### Inputs

- Default due days (positive integer, optional)
- Passing score percentage (0–100, optional)

### Processing

1. Validate admin authorization.
2. Validate input values (positive integer for due days, 0–100 for passing score).
3. Update organization training settings.
4. New tasks use updated values; existing tasks remain unchanged.
5. Create audit log entry.

### Outputs

- Updated training settings in Firestore
- Audit log entry

### Error Handling

- Non-admin user: return 403
- Invalid due days (negative or zero): reject with "Due days must be a positive integer"
- Invalid passing score (out of 0–100 range): reject with "Passing score must be between 0 and 100"

## Acceptance Criteria

1. **Given** an admin viewing training settings, **when** the settings page loads, **then** the current default due days and passing score percentage are displayed.
2. **Given** an admin updating default due days to 14, **when** saved, **then** new tasks use 14 as the default.
3. **Given** updated settings, **when** examining existing tasks, **then** they retain their original due dates and thresholds.
4. **Given** a settings change, **when** saved, **then** an audit log entry with action `training_settings.updated` is created.
5. **Given** a non-admin user, **when** they attempt to update settings, **then** access is denied.

## Traceability

| Direction | Link Type    | Target ID | Title                         |
| --------- | ------------ | --------- | ----------------------------- |
| Up        | derives-from | PRS-018    | Training compliance reporting |

## Source

**STORY-711 — Training Settings Management**

> **As a** Quality Manager **I want** to configure organization-wide training settings **So that** default behaviors match our organizational policies.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
