---
id: PRS-012
title: 'Release Workflow'
status: draft
links:
  - type: derives-from
    target: UN-005
---

# Release Workflow

## Requirement Statements

1. The system **SHALL** enforce a release lifecycle with the following state transitions: Draft → Open (In Review) → Closed (Published), with clear validation at each transition.

2. The system **SHALL** notify all required reviewers (via in-app notification and email) when a release is submitted for review (transitioned from Draft to Open).

3. The system **SHALL** prevent a release from being published until all required departmental signatures have been collected.

4. The system **SHALL** make published releases immutable — no modifications to the release record, included documents, or signatures are permitted after publication.

5. The system **SHALL** support change controls that track what changed between releases for the same scope (e.g., comparing two consecutive QMS releases).

6. The system **SHALL** provide a release list view with filtering by status (draft, open, closed) and release type (QMS, device, combined).

7. The system **SHALL** record every release lifecycle event (create, submit for review, sign, publish) as an immutable audit log entry.

## Rationale

ISO 13485 §7.3.7 requires a formal change control process for design and development changes. The release workflow ensures that document changes are reviewed by all required departments before becoming effective. Immutability after publication provides the auditable baseline that regulators expect — a published release represents a point-in-time snapshot that cannot be retroactively altered. Change controls between releases give the QA Manager visibility into what changed and why.

## Classification

| Field    | Value                                    |
| -------- | ---------------------------------------- |
| Priority | Essential                                |
| Category | Functional / Regulatory                  |
| Standard | ISO 13485 §7.3.7, FDA 21 CFR Part 820.30 |

## Acceptance Criteria

1. Given a draft release, when an Admin submits it for review, then the status changes to Open, all required reviewers receive notifications, and an audit log entry is created
2. Given an open release with 3 required department signatures and only 2 collected, when an Admin attempts to publish, then the system rejects the action with a message listing missing departments
3. Given a published release, when any user attempts to modify the release record or its associated documents, then the system denies the modification
4. Given two consecutive published QMS releases, when a user views the change control, then the system displays which documents were added, modified, or removed between the two releases
5. Given a release list view, when a user filters by status "open", then only releases in the Open (In Review) state are displayed
6. Given any release lifecycle transition, then an audit log entry is created with the release ID, acting user, previous status, new status, and timestamp

## Traceability

| Direction | Link Type    | Target ID | Title                       |
| --------- | ------------ | --------- | --------------------------- |
| Up        | derives-from | UN-005    | Release Management          |
| Down      | refined-by   | —         | _(To be linked by SWR-xxx)_ |
| Down      | verified-by  | —         | _(To be linked by TP-xxx)_  |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
