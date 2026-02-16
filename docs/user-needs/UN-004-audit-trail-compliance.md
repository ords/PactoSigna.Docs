---
id: UN-004
title: 'Audit Trail & Compliance'
status: draft
links:
  - type: derives-from
    target: US-UP-004
  - type: derives-from
    target: US-UP-001
  - type: derives-from
    target: epic-04-audit-infrastructure
---

# Audit Trail & Compliance

## Need Statement

> As an **External Auditor**, I need **to independently verify that every data modification in the QMS is recorded in a tamper-evident, immutable audit trail with authenticated user identity, server-generated timestamps, and before/after state** so that **I can confirm the organization's compliance with FDA 21 CFR Part 11 and ISO 13485 without relying on verbal explanations**.

> As a **QA Manager**, I need **to view, filter, and export audit log entries** so that **I can proactively monitor system activity, investigate incidents, and prepare audit-ready reports**.

## Context

FDA 21 CFR Part 11 mandates tamper-evident audit trails for electronic records. Every mutation in PactoSigna — organization changes, member management, document sync, release lifecycle events, signature captures, and training completions — must be recorded with the acting user's identity, a server-generated timestamp, the action performed, and the before/after state of the affected resource.

This need is cross-cutting: the audit infrastructure underlies every bounded context. The External Auditor (James Wright persona) expects to be able to independently verify records are authentic and unaltered. The QA Manager (Sarah Chen persona) uses the audit log for ongoing compliance monitoring and incident investigation.

Audit log entries must be immutable — no updates or deletes are permitted, enforced at the Firestore security rules level. Retention must meet regulatory minimums (at least 2 years), with indefinite retention as the default. The audit log viewer must support filtering by date range, user, action type, and resource type, with CSV export for offline analysis.

## Stakeholder

| Field   | Value                                                              |
| ------- | ------------------------------------------------------------------ |
| Persona | External Auditor (James Wright), QA Manager (Sarah Chen)           |
| Source  | Epic 04 — Audit Infrastructure; Product Vision §Part 11 Compliance |

## Acceptance Criteria

1. Every Cloud Function that mutates data creates an immutable audit log entry in Firestore
2. Audit entries include: action (resource.verb format), userId, userEmail, resourceType, resourceId, server-generated timestamp, and details (before/after state)
3. Firestore security rules prevent any client-side writes, updates, or deletes to audit log collections
4. Audit log viewer accessible to admins, showing entries in reverse chronological order
5. Audit log supports filtering by date range, user, action type, and resource type
6. Audit log supports search by resource ID
7. Audit log entries can be exported as CSV
8. Audit entry detail view shows before/after state as a JSON diff
9. Failed operations are also logged with error details
10. Audit logs are retained indefinitely by default, with a configurable retention period (minimum 2 years)
11. All audit actions are defined in a shared TypeScript enum for consistency across the codebase

## Traceability

| Direction | Link Type      | Target ID                    | Title                        |
| --------- | -------------- | ---------------------------- | ---------------------------- |
| Up        | derives-from   | US-UP-004                    | Auditor Persona              |
| Up        | derives-from   | US-UP-001                    | QA Manager Persona           |
| Down      | implemented-by | epic-04-audit-infrastructure | Epic 4: Audit Infrastructure |
| Down      | implemented-by | STORY-401                    | Audit Log Service            |
| Down      | implemented-by | STORY-402                    | Audit Log Viewer             |
| Down      | implemented-by | STORY-403                    | Audit Entry Detail           |
| Down      | implemented-by | STORY-404                    | Audit Log Retention          |
| Down      | implemented-by | STORY-405                    | Audit Actions Enum           |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
