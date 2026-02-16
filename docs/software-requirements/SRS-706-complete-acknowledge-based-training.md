---
id: SRS-706
title: 'Complete Acknowledge-Based Training'
status: draft
links:
  - type: derives-from
    target: PRS-017
---

# Complete Acknowledge-Based Training

## Requirement

> **SRS-706.1:** The system **SHALL** allow a member to acknowledge they have read and understood a document by providing a meaning statement (required, 1–500 characters).
>
> **SRS-706.2:** The system **SHALL** require a Part 11 signature to complete the acknowledgment (see SRS-708).
>
> **SRS-706.3:** The system **SHALL** create an audit log entry for the acknowledgment.

## Context

Acknowledge-based training is used for minor document updates where a full quiz is not required. The member reads the document content in the application and provides a meaning statement such as "I have read and understood this document." The endpoint `POST .../acknowledge` with body `{ meaning }` handles the acknowledgment.

## Classification

| Field           | Value               |
| --------------- | ------------------- |
| Safety Class    | B                   |
| Category        | Functional          |
| Bounded Context | Training Compliance |

## Detailed Specification

### Inputs

- Training task ID
- Meaning statement (1–500 characters)

### Processing

1. Validate task is in progress and is acknowledge type.
2. Validate meaning statement length (1–500 chars).
3. Record acknowledgment.
4. Require Part 11 signature for completion.
5. Create audit log entry.

### Outputs

- Acknowledgment recorded
- Audit log entry

### Error Handling

- Empty meaning statement: reject with "Meaning statement is required"
- Meaning exceeds 500 characters: reject with "Meaning must be 500 characters or fewer"
- Task not in progress: reject with status error

## Acceptance Criteria

1. **Given** a member with an acknowledge-type task, **when** they provide a valid meaning statement, **then** the acknowledgment is recorded.
2. **Given** an empty meaning statement, **when** submitted, **then** the request is rejected.
3. **Given** a meaning statement over 500 characters, **when** submitted, **then** the request is rejected.
4. **Given** a successful acknowledgment, **when** checking the audit log, **then** `training_task.acknowledged` is recorded.

## Traceability

| Direction | Link Type    | Target ID | Title                        |
| --------- | ------------ | --------- | ---------------------------- |
| Up        | derives-from | PRS-017    | Training completion workflow |

## Source

**STORY-706 — Complete Acknowledge-Based Training**

> **As a** Team Member **I want** to acknowledge that I have read and understood a document **So that** my training on minor updates is recorded.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
