---
id: TC-020
title: 'Risk Management — Frontend Tests'
status: draft
links:
  - type: verified-by
    target: SRS-309
---

# Risk Management — Frontend Tests

## 1. Purpose

Verifies the frontend risk matrix page, risk gaps accordion component, and risks API client. These tests cover the React UI layer for viewing and interacting with risk data.

## 2. Test Type

| Field         | Value                               |
| ------------- | ----------------------------------- |
| Document Type | Test Report (TEST)                  |
| Test Level    | Unit                                |
| Test Method   | Automated (Vitest)                  |
| Environment   | Local (vitest), CI (GitHub Actions) |

## 3. Scope

### In Scope

- Risk matrix page rendering (severity × likelihood grid)
- Risk gaps accordion (expandable gap details)
- Risks API client functions

### Out of Scope

- Backend risk service (see TC-019)
- Risk calculations and constants (see TC-019)

## 4. Test Coverage

| Test File                                                                     | Package         | Verifies                       |
| ----------------------------------------------------------------------------- | --------------- | ------------------------------ |
| `apps/web/src/shared/services/api/__tests__/risks-api.test.ts`                | @pactosigna/web | Risks REST API client          |
| `apps/web/src/features/risks/pages/__tests__/RiskMatrixPage.test.tsx`         | @pactosigna/web | Risk matrix page rendering     |
| `apps/web/src/features/risks/components/__tests__/RiskGapsAccordion.test.tsx` | @pactosigna/web | Risk gaps expandable component |

## 5. Pass Criteria

- Risk matrix page renders severity × likelihood grid with risk entries (SRS-309)
- Risk gaps accordion shows detected gaps with affected documents (SRS-309)
- Risks API client correctly calls risk endpoints (SRS-309)

## 6. Test Results Summary

| Metric      | Value   |
| ----------- | ------- |
| Total Cases | Pending |
| Passed      | Pending |
| Failed      | Pending |
| Pass Rate   | Pending |

## 7. Traceability

| Direction | Link Type | Target ID | Title         |
| --------- | --------- | --------- | ------------- |
| Up        | verifies  | SRS-309   | Gap Detection |
