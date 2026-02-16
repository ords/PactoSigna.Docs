---
id: SRS-506
title: 'Approve Release'
status: draft
links:
  - type: derives-from
    target: PRS-013
---

# Approve Release

## Requirement

> **SRS-506.1:** The system **SHALL** allow approval of a release only when all required department signatures have been collected.
>
> **SRS-506.2:** The system **SHALL** restrict release approval to users with the 'admin' role.
>
> **SRS-506.3:** The system **SHALL** change the release status to "approved" upon approval and record the `approvedAt` timestamp.
>
> **SRS-506.4:** The system **SHALL** create an audit log entry for the approval action.

## Context

Release approval is the quality gate that confirms all required departmental reviews are complete. Only admin users can approve, ensuring the final approver has appropriate authority. The approval status transition is irreversible under normal workflow.

## Classification

| Field           | Value              |
| --------------- | ------------------ |
| Safety Class    | B                  |
| Category        | Functional         |
| Bounded Context | Release Management |

## Detailed Specification

### Inputs

- Release ID

### Processing

1. Validate all required department signatures are collected.
2. Validate user has admin role.
3. Change release status to "approved".
4. Record `approvedAt` timestamp.
5. Create audit log entry.

### Outputs

- Release status changed to "approved"
- `approvedAt` timestamp recorded
- Audit log entry

### Error Handling

- Missing department signatures: reject with "All required departments must sign before approval"
- Non-admin user: reject with 403 Forbidden
- Release not in "in_review" status: reject with "Release must be in review to approve"

## Acceptance Criteria

1. **Given** a release with all required signatures, **when** an admin approves it, **then** the status changes to "approved".
2. **Given** a release missing one department signature, **when** approval is attempted, **then** it is rejected with an error.
3. **Given** a non-admin user, **when** they attempt to approve a release, **then** the action is rejected.
4. **Given** a successful approval, **when** examining the release, **then** `approvedAt` is populated.

## Traceability

| Direction | Link Type    | Target ID | Title                       |
| --------- | ------------ | --------- | --------------------------- |
| Up        | derives-from | PRS-013    | Release artifact generation |

## Source

**STORY-506 — Approve Release**

> **As a** Final Approver **I want** to approve a fully-signed release **So that** it can be published.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
