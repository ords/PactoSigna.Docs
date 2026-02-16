---
id: PRS-009
title: 'Audit Log Capture'
status: draft
links:
  - type: derives-from
    target: UN-004
---

# Audit Log Capture

## Requirement Statements

1. The system **SHALL** create an immutable audit log entry for every data mutation performed by Cloud Functions, including organization, member, document, release, signature, and training operations.

2. The system **SHALL** record in each audit log entry: action (in `resource.verb` format), acting user ID, acting user email, resource type, resource ID, server-generated timestamp, and a details object containing before/after state.

3. The system **SHALL** enforce immutability of audit log entries through Firestore security rules that deny all client-side writes, updates, and deletes to audit log collections.

4. The system **SHALL** log failed operations with error details in addition to successful mutations.

5. The system **SHALL** define all audit action types in a shared TypeScript enum to ensure consistency across the codebase.

6. The system **SHALL** retain audit log entries indefinitely by default, with a configurable retention period that enforces a minimum of 2 years.

7. The system **SHALL** generate server-side timestamps for all audit entries (never accepting client-provided timestamps).

## Rationale

FDA 21 CFR Part 11 §11.10(e) mandates the use of secure, computer-generated, time-stamped audit trails to independently record the date and time of operator entries and actions that create, modify, or delete electronic records. Audit trail documentation must be retained for a period at least as long as that required for the subject electronic records. Immutable, server-timestamped audit entries with before/after state provide the evidentiary foundation that auditors need to verify compliance.

## Classification

| Field    | Value                                          |
| -------- | ---------------------------------------------- |
| Priority | Essential                                      |
| Category | Regulatory                                     |
| Standard | FDA 21 CFR Part 11 §11.10(e), ISO 13485 §4.2.5 |

## Acceptance Criteria

1. Given any Cloud Function that creates, updates, or deletes a Firestore document, when the operation completes, then an audit log entry exists with the correct action, user identity, resource reference, timestamp, and before/after state
2. Given Firestore security rules, when any client (frontend or external) attempts to write to, update, or delete an audit log entry, then the operation is denied
3. Given a failed release approval (e.g., unauthorized user), when the operation fails, then an audit log entry is created with the error details and the attempted action
4. Given the audit actions enum, when a new mutation type is added to the system, then it must be added to the shared enum (enforced by TypeScript compilation)
5. Given audit log entries older than the configured retention period, then a retention policy process identifies them (but the default is indefinite retention — no automatic deletion)
6. Given any audit log entry, then the timestamp is server-generated and cannot be influenced by the client

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
