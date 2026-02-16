---
id: PRS-015
title: 'Signing Request Workflow'
status: draft
links:
  - type: derives-from
    target: UN-006
---

# Signing Request Workflow

## Requirement Statements

1. The system **SHALL** allow authorized users to create signing requests that specify the target resource (release), required signers (by department or individual), and a deadline.

2. The system **SHALL** notify requested signers via in-app notification and email when a signing request is created or when a reminder is sent.

3. The system **SHALL** track the status of each individual signer's obligation within a signing request (pending, signed, declined).

4. The system **SHALL** provide a signing request dashboard showing all active requests, their progress (number of signatures collected vs. required), and approaching deadlines.

5. The system **SHALL** allow the signing request creator to send reminders to signers who have not yet signed.

6. The system **SHALL** support external parties signing via secure, time-limited links without requiring a PactoSigna account.

7. The system **SHALL** record all signing request lifecycle events (create, notify, remind, complete, expire) in the audit log.

## Rationale

The signing request workflow operationalizes Part 11 compliance by ensuring the right people are asked to sign at the right time, with proper tracking and reminders. Without a structured request workflow, signature collection becomes ad hoc and difficult to audit. External signing support is necessary for auditors and consultants who need to provide signatures without full system access. Full audit logging of the request lifecycle provides evidence that the organization diligently pursued all required approvals.

## Classification

| Field    | Value                                          |
| -------- | ---------------------------------------------- |
| Priority | Essential                                      |
| Category | Functional                                     |
| Standard | FDA 21 CFR Part 11 §11.10(c), ISO 13485 §4.2.4 |

## Acceptance Criteria

1. Given an Admin creating a signing request for a release, when they specify departments and a deadline, then signing obligations are created for all members in those departments and notifications are sent
2. Given a signer who has not signed within 48 hours of the request, when the request creator clicks "Send Reminder", then the signer receives a reminder notification via in-app and email
3. Given a signing request, when viewed on the dashboard, then it displays the total required signatures, collected signatures, pending signatures, and time remaining until deadline
4. Given an external party (e.g., auditor without a PactoSigna account), when they receive a signing request link, then they can view the release content and provide a Part 11 compliant signature without creating an account
5. Given a signing request that reaches its deadline with incomplete signatures, then the request is flagged as overdue and the creator is notified
6. Given any signing request event, then an audit log entry is created with the event type, acting user, affected signers, and timestamp

## Traceability

| Direction | Link Type    | Target ID | Title                       |
| --------- | ------------ | --------- | --------------------------- |
| Up        | derives-from | UN-006    | Electronic Signatures       |
| Down      | refined-by   | —         | _(To be linked by SWR-xxx)_ |
| Down      | verified-by  | —         | _(To be linked by TP-xxx)_  |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
