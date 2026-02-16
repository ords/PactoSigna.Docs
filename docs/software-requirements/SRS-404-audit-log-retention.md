---
id: SRS-404
title: 'Audit Log Retention'
status: draft
links:
  - type: derives-from
    target: PRS-010
---

# Audit Log Retention

## Requirement

> **SRS-404.1:** The system **SHALL** retain audit logs indefinitely by default.
>
> **SRS-404.2:** The system **SHALL** allow organizations to configure a retention period with a minimum of 2 years, and prevent reducing the retention below the regulatory minimum.
>
> **SRS-404.3:** The system **SHALL** archive older audit logs to Cloud Storage (as JSON) while keeping recent entries (e.g., 90 days) in Firestore for fast access.
>
> **SRS-404.4:** The system **SHALL** ensure archived logs remain queryable, with acceptable latency trade-offs.

## Context

Audit log retention supports long-term compliance requirements. The `archiveAuditLogs` Cloud Function runs on a weekly schedule to move old entries from Firestore to Cloud Storage. A query function checks both Firestore and the archive to provide complete results. Initial implementation retains all logs in Firestore (archival can be deferred post-MVP).

## Classification

| Field           | Value |
| --------------- | ----- |
| Safety Class    | B     |
| Category        | Data  |
| Bounded Context | Audit |

## Detailed Specification

### Inputs

- Organization retention configuration (minimum 2 years)
- Archive schedule (weekly)

### Processing

1. `archiveAuditLogs` runs on schedule (weekly).
2. Identify entries older than the Firestore retention window (e.g., 90 days).
3. Export entries to Cloud Storage as JSON.
4. Remove archived entries from Firestore.
5. Query function transparently searches both Firestore and archive.

### Outputs

- Archived logs in Cloud Storage
- Recent logs in Firestore
- Seamless query across both stores

### Error Handling

- Retention period below minimum: reject configuration with "Retention period must be at least 2 years"
- Archive job failure: log error, retry on next schedule
- Retention policy change requires admin confirmation dialog

## Acceptance Criteria

1. **Given** default configuration, **when** audit logs are checked, **then** all logs are retained indefinitely.
2. **Given** an admin setting retention to 1 year, **when** the change is submitted, **then** it is rejected with an error about the 2-year minimum.
3. **Given** the archive job running, **when** logs older than 90 days exist, **then** they are moved to Cloud Storage and remain queryable.
4. **Given** archiving a retention policy change, **when** the admin submits, **then** a confirmation dialog is displayed before the change is applied.

## Traceability

| Direction | Link Type    | Target ID | Title                         |
| --------- | ------------ | --------- | ----------------------------- |
| Up        | derives-from | PRS-010    | Audit log viewing & filtering |

## Source

**STORY-404 — Audit Log Retention**

> **As an** organization admin **I want** audit logs retained for compliance periods **So that** we can support audits and investigations.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
