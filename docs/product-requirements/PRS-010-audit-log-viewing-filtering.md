---
id: PRS-010
title: 'Audit Log Viewing & Filtering'
status: draft
links:
  - type: derives-from
    target: UN-004
---

# Audit Log Viewing & Filtering

## Requirement Statements

1. The system **SHALL** provide an audit log viewer accessible to organization Owners and Admins, displaying entries in reverse chronological order.

2. The system **SHALL** support filtering audit log entries by date range, acting user, action type, and resource type.

3. The system **SHALL** support searching audit log entries by resource ID.

4. The system **SHALL** provide an audit entry detail view that displays the before/after state as a visual JSON diff.

5. The system **SHALL** support exporting filtered audit log entries as CSV for offline analysis and regulatory submissions.

6. The system **SHALL** paginate audit log results to maintain UI performance regardless of log volume.

## Rationale

Auditors and QA Managers need direct access to audit trails to verify compliance and investigate incidents. FDA 21 CFR Part 11 requires that audit trail documentation be available for agency review and copying. Filtering, searching, and export capabilities ensure that large audit logs remain navigable and that evidence can be extracted for regulatory submissions or incident investigations without manual data processing.

## Classification

| Field    | Value                                          |
| -------- | ---------------------------------------------- |
| Priority | Essential                                      |
| Category | Functional / Regulatory                        |
| Standard | FDA 21 CFR Part 11 §11.10(e), ISO 13485 §4.2.5 |

## Acceptance Criteria

1. Given an Admin user, when they navigate to the audit log viewer, then entries are displayed in reverse chronological order with action, user, resource, and timestamp columns
2. Given a date range filter of "2026-01-01 to 2026-01-31", when applied, then only audit entries within that range are displayed
3. Given a search by resource ID "REL-005", when submitted, then all audit entries referencing resource ID "REL-005" are returned
4. Given an audit entry for a member role change, when the detail view is opened, then the before state (e.g., `{role: "member"}`) and after state (e.g., `{role: "admin"}`) are displayed as a visual diff
5. Given a filtered set of audit entries, when the user exports as CSV, then a CSV file is downloaded containing all filtered entries with columns for action, user, resource type, resource ID, timestamp, and details
6. Given more than 50 audit entries matching a filter, when displayed, then results are paginated with navigation controls

## Traceability

| Direction | Link Type    | Target ID | Title                       |
| --------- | ------------ | --------- | --------------------------- |
| Up        | derives-from | UN-004    | Audit Trail & Compliance    |
| Down      | refined-by   | —         | _(To be linked by SWR-xxx)_ |
| Down      | verified-by  | —         | _(To be linked by TP-xxx)_  |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
