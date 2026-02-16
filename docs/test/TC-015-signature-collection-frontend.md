---
id: TC-015
title: 'Signature Collection — Frontend Tests'
status: draft
links:
  - type: verified-by
    target: SRS-604
  - type: verified-by
    target: SRS-605
  - type: verified-by
    target: SRS-607
---

# Signature Collection — Frontend Tests

## 1. Purpose

Verifies the frontend components and hooks for signature display, signing dialog, re-authentication flow, and signature history. These tests cover the React UI layer for the Signature Collection bounded context.

## 2. Test Type

| Field         | Value                               |
| ------------- | ----------------------------------- |
| Document Type | Test Report (TEST)                  |
| Test Level    | Unit                                |
| Test Method   | Automated (Vitest)                  |
| Environment   | Local (vitest), CI (GitHub Actions) |

## 3. Scope

### In Scope

- Signature panel (displays current signatures on a resource)
- Signature dialog (signing flow with meaning selection)
- Signature list component
- Re-authentication hook
- Signatures API client

### Out of Scope

- Backend signature creation (see TC-014)
- E2E signing workflows (see TC-013)

## 4. Test Coverage

| Test File                                                                        | Package         | Verifies                              |
| -------------------------------------------------------------------------------- | --------------- | ------------------------------------- |
| `apps/web/src/shared/services/api/__tests__/signatures-api.test.ts`              | @pactosigna/web | Signatures REST API client            |
| `apps/web/src/features/signatures/components/__tests__/SignaturePanel.test.tsx`  | @pactosigna/web | Signature panel on release/document   |
| `apps/web/src/features/signatures/components/__tests__/SignatureDialog.test.tsx` | @pactosigna/web | Signing dialog with meaning selection |
| `apps/web/src/features/signatures/components/__tests__/SignatureList.test.tsx`   | @pactosigna/web | Signature history list                |
| `apps/web/src/features/signatures/hooks/__tests__/useReauthenticate.test.ts`     | @pactosigna/web | Re-authentication hook for signing    |

## 5. Pass Criteria

- Signature panel shows existing signatures with signer name and timestamp (SRS-604)
- Signature dialog requires meaning selection before submission (SRS-604)
- Re-authentication hook triggers credential re-entry before sign (SRS-605)
- Signature list displays history of all signatures on a resource (SRS-607)
- API client correctly calls signature creation and listing endpoints (SRS-604)

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
| Up        | verifies  | SRS-604   | View Signature Details        |
| Up        | verifies  | SRS-605   | Re-authentication for Signing |
| Up        | verifies  | SRS-607   | View Signature History        |
