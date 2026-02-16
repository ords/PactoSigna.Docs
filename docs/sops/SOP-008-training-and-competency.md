---
id: SOP-008
title: 'Training & Competency'
status: draft
links:
  - SOP-005
---

# Training & Competency

## 1. Purpose

Define the process for managing training and competency of personnel involved in PactoSigna development and QMS activities, ensuring compliance with ISO 13485 §6.2. PactoSigna manages its own training records (dog-fooding).

## 2. Scope

Applies to all team members (including AI assistants acting under human supervision) who perform activities affecting product quality. Covers training assignment, completion tracking, competency assessment, and overdue notifications.

## 3. Definitions

| Term                 | Definition                                                                |
| -------------------- | ------------------------------------------------------------------------- |
| Training Task        | A PactoSigna entity assigned to a member for a specific document or topic |
| Quiz Session         | An assessed activity within a training task to verify understanding       |
| Competency           | Demonstrated ability to perform assigned tasks, verified via assessment   |
| Overdue Notification | Automated alert when a training task passes its due date                  |

## 4. Responsibilities

| Role                | Responsibility                                                        |
| ------------------- | --------------------------------------------------------------------- |
| Quality Manager     | Defines training requirements, reviews competency records             |
| Team Lead           | Ensures team members complete assigned training on time               |
| Team Member         | Completes assigned training tasks and quizzes within deadlines        |
| System (PactoSigna) | Auto-assigns training on release publish, sends overdue notifications |

## 5. Procedure

### 5.1 Training Identification

1. When a new SOP, work instruction, or significant document revision is published in a Release, identify affected personnel who require training.
2. Training requirements are defined by the Quality Manager based on role and document relevance.

### 5.2 Training Assignment

1. Upon release publication, the `trainingAssignmentWorker` Cloud Function automatically creates Training Tasks for affected members.
2. Each Training Task links to the specific document version and includes a due date.
3. Members receive in-app and email notifications of new assignments.

### 5.3 Training Completion

1. The team member reads the assigned document within PactoSigna.
2. If a quiz is configured, the member completes the Quiz Session.
3. Quiz definitions and correct answers are stored server-side only (ADR-018); grading is performed server-side.
4. A passing score (configured per quiz) is required to mark the training as complete.
5. The member provides an electronic signature acknowledging completion.

### 5.4 Overdue Management

1. The `trainingOverdueWorker` Cloud Function runs daily via Cloud Scheduler.
2. Overdue training tasks generate notifications to the member and their team lead.
3. Persistently overdue tasks are escalated to the Quality Manager.

### 5.5 Competency Records

1. Completed Training Tasks with quiz results serve as competency evidence.
2. The Quality Manager reviews training completion status during management reviews (SOP-010).
3. Training records are retained in Firestore and accessible via PactoSigna's training module.

## 6. Records

| Record                | Retention       | Location                  |
| --------------------- | --------------- | ------------------------- |
| Training Tasks        | Life of product | Firestore (trainingTasks) |
| Quiz Sessions         | Life of product | Firestore (quizSessions)  |
| Completion signatures | Life of product | Firestore (signatures)    |
| Overdue notifications | 3 years         | Firestore (notifications) |

## 7. References

- ISO 13485:2016 §6.2 — Human Resources / Competence, Training, and Awareness
- FDA 21 CFR Part 11 — Electronic Records; Electronic Signatures
- ADR-018 — Server-Side Compliance Data
- ADR-019 — Entity Reference Validation
- SOP-005 — Software Development Lifecycle

## Revision History

| Version | Date       | Author        | Changes       |
| ------- | ---------- | ------------- | ------------- |
| 0.1     | 2026-02-15 | PactoSigna AI | Initial draft |
