---
id: PRS-017
title: 'Training Completion Workflow'
status: draft
links:
  - type: derives-from
    target: UN-007
---

# Training Completion Workflow

## Requirement Statements

1. The system **SHALL** allow trainees to view the assigned document content within the application before completing training.

2. The system **SHALL** support quiz-based training where questions are stored server-side, never sent to the client with correct answers, and grading is performed entirely server-side.

3. The system **SHALL** randomize quiz question order and answer option order for each quiz session to prevent answer-sharing.

4. The system **SHALL** enforce a configurable passing score threshold (organization-level setting) for quiz-based training.

5. The system **SHALL** allow trainees who fail a quiz to retry after re-reading the document, with no limit on retry attempts.

6. The system **SHALL** support read-and-acknowledge training where the trainee provides a meaning statement (e.g., "I have read and understood this document") and completes a Part 11 electronic signature.

7. The system **SHALL** support instructor-led training completion where an administrator records batch sign-off with an evidence URL (e.g., link to recording) and file hash for integrity verification.

8. The system **SHALL** require a Part 11 compliant electronic signature (Trainee type) for all training completion types.

## Rationale

FDA 21 CFR 820.25 requires documented evidence of training effectiveness. ISO 13485 §6.2 requires evaluation of training effectiveness and maintenance of appropriate records. Server-side quiz storage and grading prevent circumvention of competency assessment — a Part 11 compliance requirement for ensuring that records of competency are authentic. The open-book philosophy (document visible during quiz) tests comprehension, not memorization, aligning with the goal of genuine competency. Part 11 signatures on completion seal the training record as tamper-evident evidence.

## Classification

| Field    | Value                                                        |
| -------- | ------------------------------------------------------------ |
| Priority | Essential                                                    |
| Category | Functional / Regulatory / Safety                             |
| Standard | ISO 13485 §6.2, FDA 21 CFR 820.25, FDA 21 CFR Part 11 §11.50 |

## Acceptance Criteria

1. Given a quiz-based training task, when the trainee starts the quiz, then questions are fetched from the server with answer options but without correct answer indicators
2. Given a completed quiz submission, when the server grades it, then the score is calculated server-side and the result (pass/fail with score percentage) is returned to the client
3. Given a quiz with 5 questions, when two different trainees start the quiz, then the question order and option order differ between the two sessions
4. Given an organization with a 80% passing threshold, when a trainee scores 75%, then the result is "fail" and the trainee is prompted to re-read the document before retrying
5. Given a read-and-acknowledge training task, when the trainee completes it, then they must provide a meaning statement and complete a Part 11 signature with re-authentication
6. Given an instructor-led training session, when an Admin records batch completion, then each attendee's task is marked complete with the evidence URL, file hash, and a Part 11 signature
7. Given any training completion, then a Part 11 compliant electronic signature (Trainee type) is captured and linked to the training record

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
