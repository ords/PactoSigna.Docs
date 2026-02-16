---
id: UN-007
title: 'Training & Competency Management'
status: draft
links:
  - type: derives-from
    target: US-UP-001
  - type: derives-from
    target: US-UN-001
  - type: derives-from
    target: epic-07-training-management
---

# Training & Competency Management

## Need Statement

> As a **QA Manager**, I need **the system to automatically assign training tasks to relevant team members when SOPs or work instructions are updated, track completion with Part 11 signatures, and provide a dashboard showing training status across the organization** so that **compliance with ISO 13485 §6.2 training requirements is maintained without manual tracking and we can demonstrate competency evidence to auditors**.

> As a **Team Member**, I need **to complete training tasks (quizzes, read-and-acknowledge, or instructor-led) assigned to me efficiently** so that **I can demonstrate competence on updated procedures without excessive disruption to my primary work**.

## Context

ISO 13485 §6.2 requires that personnel performing work affecting product quality are competent, and that training records are maintained as evidence. FDA 21 CFR 820.25 further requires documented training. When a Change Control becomes effective (containing SOP, Work Instruction, or Policy changes), affected staff must be trained on the updates.

Currently, training is tracked in spreadsheets, version mismatches between training records and document versions are common, and quiz integrity is compromised by paper-based assessments. PactoSigna automates this: training type (quiz for major updates, read-and-acknowledge for minor, instructor-led for workshops) is configured per document; tasks are auto-assigned to members in configured departments; quizzes are graded server-side with randomized questions for integrity; and every completion is sealed with a Part 11 electronic signature.

The QA Manager needs an admin dashboard showing aggregate statistics (pending, in-progress, completed, overdue counts), filterable by member and document. The training module follows an open-book philosophy — users can reference the document during quizzes — because the goal is to test understanding, not memorization.

## Stakeholder

| Field   | Value                                                                                               |
| ------- | --------------------------------------------------------------------------------------------------- |
| Persona | QA Manager (Sarah Chen), Team Members (all staff)                                                   |
| Source  | Epic 07 — Training Management; US-UN-001 — Training User Needs; Product Vision §Training Management |

## Acceptance Criteria

1. When a Change Control becomes effective, training tasks are automatically created for active members in the configured departments
2. Training type is determined by the document's frontmatter configuration: quiz, acknowledge, or instructor-led
3. For quiz-type training, questions are stored server-side (never sent to client with correct answers) and graded server-side
4. Quiz questions and options are randomized to prevent answer-sharing
5. Failed quizzes allow retry after re-reading the document
6. Read-and-acknowledge training requires a meaning statement and Part 11 signature
7. Instructor-led training supports batch signoff with evidence URL and hash
8. Training completion requires a Part 11 compliant electronic signature (Trainee type)
9. Admin dashboard shows aggregate training statistics with filters by status, member, and document
10. Overdue training is clearly identified and tracked
11. Due dates are configurable per organization (default: 30 days)
12. Passing score percentage is configurable per organization
13. Duplicate tasks are prevented: no new task if a pending/in-progress task exists for the same member and document

## Traceability

| Direction | Link Type      | Target ID                   | Title                           |
| --------- | -------------- | --------------------------- | ------------------------------- |
| Up        | derives-from   | US-UP-001                   | QA Manager Persona              |
| Up        | derives-from   | US-UN-001                   | Training User Needs (Usability) |
| Down      | implemented-by | epic-07-training-management | Epic 7: Training Management     |
| Down      | implemented-by | STORY-701                   | Auto-Assign Training on CC      |
| Down      | implemented-by | STORY-702                   | View My Training Tasks          |
| Down      | implemented-by | STORY-705                   | Complete Quiz-Based Training    |
| Down      | implemented-by | STORY-706                   | Complete Acknowledge Training   |
| Down      | implemented-by | STORY-707                   | Instructor-Led Batch Signoff    |
| Down      | implemented-by | STORY-708                   | Part 11 Signature on Completion |
| Down      | implemented-by | STORY-709                   | Admin Training Dashboard        |
| Down      | implemented-by | STORY-711                   | Training Settings Management    |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
