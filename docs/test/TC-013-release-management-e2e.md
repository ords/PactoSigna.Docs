---
id: TC-013
title: 'Release Management — E2E Tests'
status: draft
links:
  - type: verified-by
    target: SRS-501
  - type: verified-by
    target: SRS-503
  - type: verified-by
    target: SRS-506
  - type: verified-by
    target: SRS-507
---

# Release Management — E2E Tests

## 1. Purpose

Verifies the full release lifecycle through end-to-end Playwright BDD journeys. These tests exercise the complete flow from release creation through signing, approval, and publishing across the frontend and backend together.

## 2. Test Type

| Field         | Value                                    |
| ------------- | ---------------------------------------- |
| Document Type | Test Report (TEST)                       |
| Test Level    | System (E2E)                             |
| Test Method   | Automated (Playwright BDD / Gherkin)     |
| Environment   | Firebase Emulators + Vite dev server, CI |

## 3. Scope

### In Scope

- E2E: Release workflow journey (create → sign → approve → publish)
- E2E: Release lifecycle journey (draft → review → approval states)
- Cross-cutting: Signature collection within release context

### Out of Scope

- Backend unit tests (see TC-011)
- Frontend unit tests (see TC-012)

## 4. Test Coverage

| Test File                                            | Package | Verifies                              |
| ---------------------------------------------------- | ------- | ------------------------------------- |
| `e2e/features/journeys/03-release-workflow.feature`  | e2e     | Release signing and approval workflow |
| `e2e/features/journeys/08-release-lifecycle.feature` | e2e     | Full release state transitions        |

## 5. Pass Criteria

- User can create a release from the UI and see it in draft status (SRS-501)
- User can submit release for review and status transitions correctly (SRS-503)
- Required signers can sign and release moves to approved (SRS-506)
- Approved release can be published and becomes immutable (SRS-507)
- All state transitions are reflected in the UI in real time

## 6. Test Results Summary

| Metric      | Value   |
| ----------- | ------- |
| Total Cases | Pending |
| Passed      | Pending |
| Failed      | Pending |
| Pass Rate   | Pending |

## 7. Traceability

| Direction | Link Type | Target ID | Title                     |
| --------- | --------- | --------- | ------------------------- |
| Up        | verifies  | SRS-501   | Create Release Bundle     |
| Up        | verifies  | SRS-503   | Submit Release for Review |
| Up        | verifies  | SRS-506   | Approve Release           |
| Up        | verifies  | SRS-507   | Publish Release           |
