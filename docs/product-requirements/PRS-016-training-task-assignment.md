---
id: PRS-016
title: 'Training Task Assignment'
status: draft
links:
  - type: derives-from
    target: UN-007
---

# Training Task Assignment

## Requirement Statements

1. The system **SHALL** automatically create training tasks for active members in configured departments when a Change Control becomes effective and contains SOP, Work Instruction, or Policy document changes.

2. The system **SHALL** determine training type based on the document's configuration: quiz for major updates (first-time and significant changes), read-and-acknowledge for minor updates, and instructor-led for workshop-based training.

3. The system **SHALL** allow administrators to manually assign training tasks to specific members for any document, independent of the release cycle.

4. The system **SHALL** prevent duplicate training tasks — if a pending or in-progress task already exists for the same member and document, no new task is created.

5. The system **SHALL** assign a configurable due date to each training task (organization default: 30 days from assignment).

6. The system **SHALL** notify assigned members of new training tasks via in-app notification and email, including the document title, training type, and due date.

## Rationale

ISO 13485 §6.2 requires organizations to determine necessary competency for personnel performing work affecting product quality, provide training to achieve that competency, and maintain records. FDA 21 CFR 820.25 additionally requires that training be documented. Automatic assignment on Change Control effectiveness ensures no training obligation is missed when procedures are updated. Manual assignment provides flexibility for ad hoc training needs such as new hires or role changes.

## Classification

| Field    | Value                             |
| -------- | --------------------------------- |
| Priority | Essential                         |
| Category | Functional / Regulatory           |
| Standard | ISO 13485 §6.2, FDA 21 CFR 820.25 |

## Acceptance Criteria

1. Given a published release containing SOP-001 changes with departments "Quality" and "Engineering" configured for training, when the release is published, then training tasks are created for all active members in Quality and Engineering departments
2. Given a document configured as "quiz" training type, when training tasks are auto-assigned, then each task is created with type "quiz"
3. Given an Admin, when they manually assign training for REQ-001 to a specific member, then a training task is created regardless of whether a release triggered it
4. Given a member who already has a pending training task for SOP-001, when a new release publishes SOP-001 changes, then no duplicate task is created
5. Given an organization with a 14-day training due date configured, when tasks are assigned, then each task's due date is set to 14 days from the assignment date
6. Given a new training task is assigned, when the assignment completes, then the assigned member receives an in-app notification and email with the document title, training type, and due date

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
