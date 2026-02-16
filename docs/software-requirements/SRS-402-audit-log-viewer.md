---
id: SRS-402
title: 'Audit Log Viewer'
status: draft
links:
  - type: derives-from
    target: PRS-009
---

# Audit Log Viewer

## Requirement

> **SRS-402.1:** The system **SHALL** provide an audit log viewer accessible only to users with the admin role, displaying entries in reverse chronological order.
>
> **SRS-402.2:** The system **SHALL** support filtering by date range, user, action type, and resource type, and searching by resource ID.
>
> **SRS-402.3:** The system **SHALL** support pagination for large audit logs and allow exporting filtered results as CSV.

## Context

The audit log viewer is an admin-only interface for reviewing system activity. Firestore composite indexes are used for efficient filtered queries. Cursor-based pagination provides performance for large datasets.

## Classification

| Field           | Value      |
| --------------- | ---------- |
| Safety Class    | A          |
| Category        | Functional |
| Bounded Context | Audit      |

## Detailed Specification

### Inputs

- Filter criteria: date range, user, action type, resource type
- Search term: resource ID
- Pagination cursor

### Processing

1. Validate admin authorization.
2. Build Firestore query with selected filters.
3. Apply cursor-based pagination.
4. Return matching audit entries.

### Outputs

- Paginated list of audit entries
- CSV export of filtered results

### Error Handling

- Non-admin access: return 403 Forbidden
- No entries matching filter: display "No audit entries found"

## Acceptance Criteria

1. **Given** an admin viewing the audit log, **when** entries exist, **then** they are displayed in reverse chronological order.
2. **Given** an admin filtering by date range "Jan 10 - Jan 13", **when** the filter is applied, **then** only entries within that range are shown.
3. **Given** an admin filtering by user "jane@co.com", **when** the filter is applied, **then** only entries by that user are shown.
4. **Given** a non-admin user, **when** they attempt to access the audit log viewer, **then** access is denied.
5. **Given** filtered audit results, **when** the admin clicks "Export CSV", **then** a CSV file is downloaded.

## Traceability

| Direction | Link Type    | Target ID | Title             |
| --------- | ------------ | --------- | ----------------- |
| Up        | derives-from | PRS-009    | Audit log capture |

## Source

**STORY-402 — Audit Log Viewer**

> **As an** organization admin **I want** to view the audit log **So that** I can review system activity for compliance.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
