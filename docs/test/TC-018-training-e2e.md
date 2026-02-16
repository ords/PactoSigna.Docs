---
id: TC-018
title: 'Training Management — E2E Tests'
status: draft
links:
  - type: verified-by
    target: SRS-701
  - type: verified-by
    target: SRS-705
  - type: verified-by
    target: SRS-706
  - type: verified-by
    target: SRS-708
---

# Training Management — E2E Tests

## 1. Purpose

Verifies the full training compliance workflow through end-to-end Playwright BDD journeys. Tests exercise training assignment, task completion (quiz and acknowledge), and Part 11 signature on completion.

## 2. Test Type

| Field         | Value                                    |
| ------------- | ---------------------------------------- |
| Document Type | Test Report (TEST)                       |
| Test Level    | System (E2E)                             |
| Test Method   | Automated (Playwright BDD / Gherkin)     |
| Environment   | Firebase Emulators + Vite dev server, CI |

## 3. Scope

### In Scope

- E2E: Training compliance journey (auto-assign → view → complete)
- E2E: Training completion journey (start → quiz/acknowledge → sign)
- Cross-cutting: Part 11 signature during training completion

### Out of Scope

- Backend unit tests (see TC-016)
- Frontend unit tests (see TC-017)

## 4. Test Coverage

| Test File                                              | Package | Verifies                                         |
| ------------------------------------------------------ | ------- | ------------------------------------------------ |
| `e2e/features/journeys/06-training-compliance.feature` | e2e     | Training auto-assignment and compliance tracking |
| `e2e/features/journeys/09-training-completion.feature` | e2e     | Training task completion with signature          |

## 5. Pass Criteria

- Training tasks appear for users after release publish triggers assignment (SRS-701)
- User can complete a quiz-based training task (SRS-705)
- User can complete an acknowledge-based training task (SRS-706)
- Training completion requires Part 11 electronic signature (SRS-708)
- Completed tasks show as done in the user's training list

## 6. Test Results Summary

| Metric      | Value   |
| ----------- | ------- |
| Total Cases | Pending |
| Passed      | Pending |
| Failed      | Pending |
| Pass Rate   | Pending |

## 7. Traceability

| Direction | Link Type | Target ID | Title                                                |
| --------- | --------- | --------- | ---------------------------------------------------- |
| Up        | verifies  | SRS-701   | Auto-Assign Training on Change Control Effectiveness |
| Up        | verifies  | SRS-705   | Complete Quiz-Based Training                         |
| Up        | verifies  | SRS-706   | Complete Acknowledge-Based Training                  |
| Up        | verifies  | SRS-708   | Part 11 Signature on Training Completion             |
