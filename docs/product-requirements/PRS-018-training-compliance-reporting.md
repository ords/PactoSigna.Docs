---
id: PRS-018
title: 'Training Compliance Reporting'
status: draft
links:
  - type: derives-from
    target: UN-007
---

# Training Compliance Reporting

## Requirement Statements

1. The system **SHALL** provide an administrator training dashboard showing aggregate statistics: total tasks, pending, in-progress, completed, and overdue counts.

2. The system **SHALL** clearly identify overdue training tasks — tasks that have passed their due date without completion — with visual indicators and counts.

3. The system **SHALL** support filtering the training dashboard by status (pending, in-progress, completed, overdue), by member, and by document.

4. The system **SHALL** send automated notifications for overdue training tasks to the assigned member and their organization administrators on a configurable schedule.

5. The system **SHALL** provide a training matrix view showing members as rows and documents as columns, with completion status in each cell, exportable as CSV for audit submissions.

6. The system **SHALL** provide each member a personal "My Training" view showing their assigned tasks, completion status, due dates, and historical records.

## Rationale

ISO 13485 §6.2 requires that records of training, skills, and experience are maintained and available for review. Auditors expect to see a complete training matrix demonstrating that all personnel affecting product quality have been trained on current procedures. Overdue tracking and automated reminders ensure training obligations are not overlooked, and the dashboard gives QA Managers proactive visibility into compliance status across the organization.

## Classification

| Field    | Value                             |
| -------- | --------------------------------- |
| Priority | Essential                         |
| Category | Functional / Regulatory           |
| Standard | ISO 13485 §6.2, FDA 21 CFR 820.25 |

## Acceptance Criteria

1. Given an Admin viewing the training dashboard, then aggregate counts for pending, in-progress, completed, and overdue tasks are displayed prominently
2. Given 5 overdue training tasks, when the dashboard loads, then those tasks are visually highlighted (e.g., red badge) and the overdue count shows "5"
3. Given a filter by member "Alex Park", when applied, then only Alex Park's training tasks are displayed with their statuses
4. Given a training task that becomes overdue, then the system sends a notification to the assigned member and organization admins (with configurable frequency — default: daily until resolved)
5. Given the training matrix view, when exported as CSV, then the file contains a row per member, a column per document, and each cell shows the completion status (completed date, pending, overdue, or N/A)
6. Given a member viewing "My Training", then they see their assigned tasks sorted by due date with clear status indicators, and can access completion history

## Traceability

| Direction | Link Type    | Target ID | Title                            |
| --------- | ------------ | --------- | -------------------------------- |
| Up        | derives-from | UN-007    | Training & Competency Management |
| Down      | refined-by   | —         | _(To be linked by SWR-xxx)_      |
| Down      | verified-by  | —         | _(To be linked by TP-xxx)_       |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
