---
id: SOP-003
title: 'Design Control'
status: draft
links:
  - SOP-004
  - SOP-005
  - SOP-007
---

# Design Control

## 1. Purpose

Define the design and development process for PactoSigna, ensuring that user needs are systematically translated into verified and validated software, in compliance with ISO 13485 §7.3.

## 2. Scope

Applies to all design and development activities for PactoSigna, from initial user needs capture through design verification and validation. Covers all team members involved in product design, development, and quality.

## 3. Definitions

| Term                       | Definition                                                                        |
| -------------------------- | --------------------------------------------------------------------------------- |
| User Need (UN)             | A statement of what users require, captured in `docs/personas/`                   |
| Product Requirement (PR)   | A measurable requirement derived from user needs, in `docs/product-requirements/` |
| Software Requirement (SWR) | A detailed technical requirement, in `docs/software-requirements/`                |
| Design Output              | Architecture docs, detailed design, source code                                   |
| Design Verification        | Confirmation that outputs meet requirements (testing)                             |
| Design Validation          | Confirmation that the product meets user needs (acceptance testing)               |

## 4. Responsibilities

| Role               | Responsibility                                                |
| ------------------ | ------------------------------------------------------------- |
| Product Manager    | Captures user needs, writes product requirements              |
| Software Architect | Translates requirements to architecture and detailed design   |
| Developer          | Implements design outputs, writes unit/integration tests      |
| Quality Manager    | Ensures traceability, reviews verification/validation results |

## 5. Procedure

### 5.1 Design Planning

1. For each feature or release, create a GitHub Issue with acceptance criteria.
2. During brainstorming (SOP-001), produce: user needs analysis, product requirements, and UX artifacts.
3. Document the design plan as a GitHub Issue with linked sub-issues for each design phase.

### 5.2 Design Input

1. Capture user needs in persona documents (`docs/personas/`).
2. Derive product requirements (`PR-xxx`) with traceability links to user needs.
3. Derive software requirements (`SWR-xxx`) with traceability links to product requirements.
4. Review all requirements via Pull Request before implementation begins.

### 5.3 Design Output

1. Produce architecture documents: HLD (`docs/architecture/hld-*.md`) and SDDs (`docs/architecture/sdd-*.md`).
2. Record architectural decisions as ADRs (`docs/architecture/adr/`).
3. Implement source code in feature branches following SOP-005.
4. All design outputs must be traceable to their corresponding design inputs.

### 5.4 Design Review

1. Conduct design reviews at each phase transition via Pull Request review.
2. Reviewers verify: completeness, traceability, regulatory compliance, and risk coverage.
3. Document review outcomes in PR comments; approval constitutes review sign-off.

### 5.5 Design Verification

1. Execute verification activities per SOP-007: unit tests, integration tests, E2E tests.
2. Record test results in CI pipeline logs and coverage reports.
3. Verify that all software requirements have corresponding test cases.

### 5.6 Design Validation

1. Perform acceptance testing against user needs and product requirements.
2. Execute E2E test suites that map to user journeys.
3. Record validation results and any deviations as GitHub Issues.

### 5.7 Design Transfer

1. Merge verified and validated code to `main` via approved Pull Request.
2. Deploy through the standard release process (SOP-005 §5.5).
3. Ensure all design history records are complete before release.

## 6. Records

| Record                 | Retention       | Location                      |
| ---------------------- | --------------- | ----------------------------- |
| User needs             | Life of product | `docs/personas/`              |
| Product requirements   | Life of product | `docs/product-requirements/`  |
| Software requirements  | Life of product | `docs/software-requirements/` |
| Architecture documents | Life of product | `docs/architecture/`          |
| Design review records  | Life of product | GitHub Pull Requests          |
| Test results           | 3 years         | CI pipeline logs              |

## 7. References

- ISO 13485:2016 §7.3 — Design and Development
- IEC 62304:2006 §5 — Software Development Process
- SOP-004 — Risk Management
- SOP-005 — Software Development Lifecycle
- SOP-007 — Verification & Validation

## Revision History

| Version | Date       | Author        | Changes       |
| ------- | ---------- | ------------- | ------------- |
| 0.1     | 2026-02-15 | PactoSigna AI | Initial draft |
