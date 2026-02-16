---
id: TC-014
title: 'Signature Collection — Backend Tests'
status: draft
links:
  - type: verified-by
    target: SRS-601
  - type: verified-by
    target: SRS-602
  - type: verified-by
    target: SRS-603
  - type: verified-by
    target: SRS-605
  - type: verified-by
    target: SRS-606
---

# Signature Collection — Backend Tests

## 1. Purpose

Verifies the backend signature service and signing requests service. These tests ensure electronic signatures comply with Part 11 requirements: immutability, re-authentication, meaning selection, and ownership validation.

## 2. Test Type

| Field         | Value                               |
| ------------- | ----------------------------------- |
| Document Type | Test Report (TEST)                  |
| Test Level    | Unit                                |
| Test Method   | Automated (Vitest)                  |
| Environment   | Local (vitest), CI (GitHub Actions) |

## 3. Scope

### In Scope

- Signature creation with meaning and authentication
- Signature immutability enforcement
- Signature ownership and resource validation (ADR-019)
- Role-based signing (department reviewer, author, final approver)
- Signing request creation and management
- Signature eligibility logic

### Out of Scope

- Frontend signature UI (see TC-015)
- E2E signing flow (see TC-013)

## 4. Test Coverage

| Test File                                                                   | Package              | Verifies                                     |
| --------------------------------------------------------------------------- | -------------------- | -------------------------------------------- |
| `apps/services/src/modules/signatures/SignaturesService.test.ts`            | @pactosigna/services | Signature creation, validation, immutability |
| `apps/services/src/modules/signing-requests/SigningRequestsService.test.ts` | @pactosigna/services | Signing request lifecycle                    |
| `apps/web/src/shared/utils/signature-eligibility.test.ts`                   | @pactosigna/web      | Signature eligibility determination logic    |

## 5. Pass Criteria

- Department reviewer can sign with correct role and meaning (SRS-601)
- Author can sign with "I authored this" meaning (SRS-602)
- Final approver can sign to approve release (SRS-603)
- Re-authentication is required before signing (SRS-605)
- Signature validation checks ownership and resource binding (SRS-606)
- Signatures are immutable once created (Part 11 compliance)
- Entity references are validated per ADR-019

## 6. Test Results Summary

| Metric      | Value   |
| ----------- | ------- |
| Total Cases | Pending |
| Passed      | Pending |
| Failed      | Pending |
| Pass Rate   | Pending |

## 7. Traceability

| Direction | Link Type | Target ID | Title                         |
| --------- | --------- | --------- | ----------------------------- |
| Up        | verifies  | SRS-601   | Sign as Department Reviewer   |
| Up        | verifies  | SRS-602   | Sign as Author                |
| Up        | verifies  | SRS-603   | Sign as Final Approver        |
| Up        | verifies  | SRS-605   | Re-authentication for Signing |
| Up        | verifies  | SRS-606   | Signature Validation          |
