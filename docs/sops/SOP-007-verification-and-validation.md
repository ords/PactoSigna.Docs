---
id: SOP-007
title: 'Verification & Validation'
status: draft
links:
  - SOP-003
  - SOP-005
---

# Verification & Validation

## 1. Purpose

Define the verification and validation (V&V) process for PactoSigna software, ensuring that software products conform to specifications and fulfill their intended use, per ISO 13485 §7.3.6 and §7.3.7.

## 2. Scope

Applies to all software verification (testing against requirements) and validation (testing against user needs) activities. Covers unit, integration, end-to-end, and manual testing across all apps and packages.

## 3. Definitions

| Term          | Definition                                                            |
| ------------- | --------------------------------------------------------------------- |
| Verification  | Confirmation that design outputs meet design inputs (requirements)    |
| Validation    | Confirmation that the final product meets user needs and intended use |
| Unit Test     | Automated test of an isolated function or component (Vitest)          |
| E2E Test      | End-to-end test simulating user workflows (Playwright + BDD/Gherkin)  |
| Test Coverage | Percentage of source code exercised by automated tests                |

## 4. Responsibilities

| Role             | Responsibility                                               |
| ---------------- | ------------------------------------------------------------ |
| Developer        | Writes unit and integration tests alongside implementation   |
| Quality Manager  | Defines test strategy, reviews coverage, approves test plans |
| AI Assistant     | Generates test cases, identifies coverage gaps               |
| All Team Members | Report defects found during any testing activity             |

## 5. Procedure

### 5.1 Test Planning

1. Each GitHub Issue with acceptance criteria implicitly defines required test cases.
2. Map software requirements (`SWR-xxx`) to test cases to maintain traceability.
3. Maintain the test strategy in `docs/testing/best-practices.md`.

### 5.2 Unit Testing (Verification)

1. Write unit tests using Vitest for all business logic in `packages/shared`, `packages/contracts`, `packages/data`, and backend services.
2. Run tests with `pnpm test` before every commit.
3. Target meaningful coverage of critical paths; check with `pnpm test:coverage`.
4. Firestore composite index tests must accompany any new queries (`firestore-indexes.test.ts`).

### 5.3 End-to-End Testing (Validation)

1. Write E2E tests using Playwright with BDD (Gherkin) syntax in `e2e/features/`.
2. Tests are organized as user journeys with worker isolation for parallel execution.
3. Run E2E tests with `pnpm e2e` (requires emulators and dev server running via `pnpm dev:e2e`).
4. E2E tests validate user workflows against product requirements and user needs.

### 5.4 Continuous Integration

1. The CI pipeline executes all verification tests on every Pull Request.
2. PRs with failing tests must not be merged.
3. Test results are recorded in GitHub Actions logs.

### 5.5 Defect Management

1. Record defects discovered during testing as GitHub Issues with the label `bug`.
2. Bugs follow the standard issue workflow (SOP-001) with root cause analysis.
3. Regression tests must be added for every bug fix.

### 5.6 Release Verification

1. Before each release, confirm all automated tests pass on the release candidate.
2. Review test coverage reports for regressions.
3. Record V&V summary in the release documentation.

## 6. Records

| Record           | Retention       | Location            |
| ---------------- | --------------- | ------------------- |
| Test source code | Life of product | GitHub repository   |
| CI test results  | 1 year          | GitHub Actions logs |
| Coverage reports | 1 year          | CI artifacts        |
| Defect records   | Life of product | GitHub Issues       |

## 7. References

- ISO 13485:2016 §7.3.6 — Design and Development Verification
- ISO 13485:2016 §7.3.7 — Design and Development Validation
- IEC 62304:2006 §5.5 — Software Integration and Integration Testing
- SOP-003 — Design Control
- SOP-005 — Software Development Lifecycle

## Revision History

| Version | Date       | Author        | Changes       |
| ------- | ---------- | ------------- | ------------- |
| 0.1     | 2026-02-15 | PactoSigna AI | Initial draft |
