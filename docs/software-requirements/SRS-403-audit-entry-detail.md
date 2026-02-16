---
id: SRS-403
title: 'Audit Entry Detail'
status: draft
links:
  - type: derives-from
    target: PRS-009
---

# Audit Entry Detail

## Requirement

> **SRS-403.1:** The system **SHALL** display the full details of a selected audit entry including before and after state as a JSON diff view.
>
> **SRS-403.2:** The system **SHALL** display all metadata associated with the entry and provide links to related resources if they exist.
>
> **SRS-403.3:** The system **SHALL** render the audit entry detail as read-only with no modification capability.

## Context

Audit entry detail provides deep inspection of individual audit records for compliance investigations. The before/after state comparison uses a JSON diff viewer. All entries are immutable and displayed read-only.

## Classification

| Field           | Value      |
| --------------- | ---------- |
| Safety Class    | B          |
| Category        | Functional |
| Bounded Context | Audit      |

## Detailed Specification

### Inputs

- Audit entry ID

### Processing

1. Fetch audit entry from Firestore.
2. Parse before/after state for diff display.
3. Resolve resource links (if resource IDs reference existing entities).
4. Render as read-only view.

### Outputs

- Full audit entry details
- JSON diff of before/after state
- Links to related resources

### Error Handling

- Entry not found: display 404
- Referenced resource deleted: display "(resource no longer exists)"

## Acceptance Criteria

1. **Given** an admin clicking an audit entry, **when** the detail view loads, **then** the full entry is displayed with all metadata.
2. **Given** an entry with before and after state, **when** viewing details, **then** a JSON diff shows the changes.
3. **Given** an entry referencing an existing resource, **when** viewing details, **then** the resource is linked and navigable.
4. **Given** any audit entry detail view, **when** examining the UI, **then** no edit or delete controls are present.

## Traceability

| Direction | Link Type    | Target ID | Title             |
| --------- | ------------ | --------- | ----------------- |
| Up        | derives-from | PRS-009    | Audit log capture |

## Source

**STORY-403 — Audit Entry Detail**

> **As an** organization admin **I want** to view details of an audit entry **So that** I can understand exactly what changed.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
