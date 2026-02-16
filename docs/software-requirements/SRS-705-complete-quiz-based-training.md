---
id: SRS-705
title: 'Complete Quiz-Based Training'
status: draft
links:
  - type: derives-from
    target: PRS-017
---

# Complete Quiz-Based Training

## Requirement

> **SRS-705.1:** The system **SHALL** create a quiz session with randomized question and option order (using seed-based approach) when a member starts a quiz, presenting questions without correct answers.
>
> **SRS-705.2:** The system **SHALL** support question types: single-choice, multiple-choice, and true-false.
>
> **SRS-705.3:** The system **SHALL** perform all quiz scoring entirely server-side (ADR-018), returning: passed/failed, score percentage, correct count, total count, and per-question explanations.
>
> **SRS-705.4:** The system **SHALL** allow retry (start new session) for failed quizzes, and require a Part 11 signature to complete passed quizzes (see SRS-708).

## Context

Quiz-based training provides objective competence assessment. The quiz definition is stored server-side in the training task (per ADR-018) to prevent client access to correct answers. Randomization uses a seed for reproducibility. The endpoints are `POST .../quiz/start` and `POST .../quiz/submit`. Passing score is configurable per organization. Request body for submission: `{ sessionId, answers }`.

## Classification

| Field           | Value               |
| --------------- | ------------------- |
| Safety Class    | B                   |
| Category        | Functional          |
| Bounded Context | Training Compliance |

## Detailed Specification

### Inputs

- Training task ID (for quiz start)
- Session ID and answers map (for quiz submit)

### Processing

1. **Start quiz**: Create quiz session with randomized question/option order using seed.
2. Return questions without correct answers (only question text and options).
3. **Submit quiz**: Load quiz definition from task, score answers server-side.
4. Calculate score, determine pass/fail against organization threshold.
5. Return results with per-question explanations.

### Outputs

- Quiz session document in Firestore
- Score result: passed/failed, score %, correct/total counts
- Per-question explanations after submission

### Error Handling

- Task not in progress: reject start
- Invalid session ID: reject submission
- Answers format mismatch: reject with validation error

## Acceptance Criteria

1. **Given** a member starting a quiz, **when** the session is created, **then** questions are presented with randomized order and without correct answers.
2. **Given** submitted answers, **when** scored server-side, **then** the result includes passed/failed, score percentage, and per-question explanations.
3. **Given** a failed quiz, **when** the member retries, **then** a new quiz session is created with fresh randomization.
4. **Given** a passed quiz, **when** the result is returned, **then** the system requires a Part 11 signature to complete the task.

## Traceability

| Direction | Link Type    | Target ID | Title                        |
| --------- | ------------ | --------- | ---------------------------- |
| Up        | derives-from | PRS-017    | Training completion workflow |

## Source

**STORY-705 — Complete Quiz-Based Training**

> **As a** Team Member **I want** to complete a quiz to demonstrate my understanding of a document **So that** my competence is objectively assessed.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
