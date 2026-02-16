---
id: TC-019
title: 'Risk Management — Backend Tests'
status: draft
links:
  - type: verified-by
    target: SRS-309
---

# Risk Management — Backend Tests

## 1. Purpose

Verifies the backend risk management service, controller, and shared risk logic including gap detection and risk matrix calculations. These tests ensure the Risk Management bounded context's server-side requirements are met.

## 2. Test Type

| Field         | Value                               |
| ------------- | ----------------------------------- |
| Document Type | Test Report (TEST)                  |
| Test Level    | Unit                                |
| Test Method   | Automated (Vitest)                  |
| Environment   | Local (vitest), CI (GitHub Actions) |

## 3. Scope

### In Scope

- Risk service: risk entry CRUD, risk matrix queries
- Risk controller: HTTP endpoint handling
- Risk gaps detection logic
- Risk matrix constants and calculations
- Gap detection shared function (Cloud Functions)

### Out of Scope

- Frontend risk UI (see TC-020)
- Risk integration with document traceability (see TC-008)

## 4. Test Coverage

| Test File                                                 | Package               | Verifies                                        |
| --------------------------------------------------------- | --------------------- | ----------------------------------------------- |
| `apps/services/src/modules/risks/RisksService.test.ts`    | @pactosigna/services  | Risk entry management                           |
| `apps/services/src/modules/risks/riskGaps.test.ts`        | @pactosigna/services  | Risk gap detection algorithms                   |
| `apps/services/src/modules/risks/RisksController.test.ts` | @pactosigna/services  | Risk HTTP endpoint handling                     |
| `apps/functions/src/shared/gap-detection.test.ts`         | @pactosigna/functions | Shared gap detection logic                      |
| `packages/shared/src/constants/risk-matrix.test.ts`       | @pactosigna/shared    | Risk matrix constants and severity calculations |

## 5. Pass Criteria

- Risk service creates and retrieves risk entries with severity/likelihood (SRS-309)
- Risk gaps are detected when mitigations are missing (SRS-309)
- Risk matrix constants produce correct severity levels (SRS-309)
- Gap detection identifies unlinked risks across documents (SRS-309)
- Risk controller validates request schemas and returns proper responses

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
