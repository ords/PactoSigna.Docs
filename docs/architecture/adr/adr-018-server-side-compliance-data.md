# ADR-018: Server-Side Source of Truth for Compliance-Critical Data

## Status

Accepted

## Date

2026-02-04

## Context

PactoSigna is a QMS system requiring FDA 21 CFR Part 11 compliance. A security review of the Training module identified a critical vulnerability: quiz definitions containing correct answers were being sent from the client and used for server-side scoring. This allowed attackers to manipulate passing thresholds or correct answers to fraudulently pass training assessments.

This vulnerability pattern can occur whenever:

1. Scoring/grading logic depends on client-provided "correct" answers
2. Pass/fail thresholds are client-controlled
3. Assessment configurations are sent with submissions

For Part 11 compliance, training completion records are legally binding. Fraudulent completion undermines the entire quality management system.

## Decision

**All compliance-critical data used for scoring, grading, or validation must be stored server-side and never trusted from client requests.**

### What Constitutes Compliance-Critical Data

1. **Correct answers** for quizzes and assessments
2. **Passing thresholds** (minimum scores, required percentages)
3. **Assessment configurations** that affect outcomes (time limits, retry policies)
4. **Grading rubrics** and scoring algorithms
5. **Validation rules** that determine compliance status

### Implementation Pattern

```
┌─────────────┐                    ┌─────────────────┐
│   Client    │                    │     Server      │
└─────────────┘                    └─────────────────┘
       │                                   │
       │  Submit answers only              │
       │  (no correct answers,             │
       │   no passing threshold)           │
       │ ─────────────────────────────────▶│
       │                                   │
       │                                   │ Fetch quiz definition
       │                                   │ from TrainingTask or
       │                                   │ Document (server-side)
       │                                   │
       │                                   │ Score answers against
       │                                   │ server-stored correct
       │                                   │ answers
       │                                   │
       │  Return: score, passed,           │
       │  correctAnswers (for review)      │
       │ ◀─────────────────────────────────│
       │                                   │
```

### Specific Changes for Training Module

1. **Quiz Definition Storage**: Store `quizDefinition` in `TrainingTask` document when task is created
2. **API Schema Changes**: Remove `quizDefinition` from `StartQuizRequest` and `SubmitQuizRequest` schemas
3. **Server-Side Scoring**: `TrainingService.submitQuiz()` fetches quiz definition from task, not request

## Consequences

### Positive

- **Fraud Prevention**: Attackers cannot manipulate scoring criteria
- **Audit Integrity**: Scores are verifiably calculated by trusted server
- **Part 11 Compliance**: Assessment results are legally defensible
- **Consistent Behavior**: Same quiz always scored the same way

### Negative

- **Storage Overhead**: Quiz definitions duplicated in each training task
- **Update Complexity**: Changing a quiz requires considering in-progress tasks
- **Migration Required**: Existing code must be refactored

### Neutral

- **API Simplification**: Smaller request payloads (no quiz definition)
- **Test Updates**: Test fixtures must set up server-side data

## Implementation Checklist

- [ ] Add `quizDefinition` field to `TrainingTask` model
- [ ] Store quiz definition when creating training task
- [ ] Remove `quizDefinition` from request schemas
- [ ] Update `startQuiz` to fetch definition from task
- [ ] Update `submitQuiz` to fetch definition from task
- [ ] Update frontend to not send quiz definitions

## Related Decisions

- [ADR-008: Backend-Only Data Access](adr-008-backend-only-firestore-writes.md) - All writes through backend
- [ADR-019: Entity Reference Validation](adr-019-entity-reference-validation.md) - Validating entity ownership
