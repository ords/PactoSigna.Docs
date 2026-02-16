---
id: RISK-US-001
title: 'Complex Release Workflow'
status: draft
links:
  - type: derives-from
    target: UN-005
  - type: mitigated-by
    target: SDD-008
---

# Complex Release Workflow

## 1. Purpose

This document assesses the usability risk that users misunderstand the multi-step release approval flow, leading to unsigned or incorrectly signed releases. This compromises regulatory compliance for the QMS SaMD per IEC 62366-1 and ISO 14971.

## 2. Risk Category

| Field       | Value                                     |
| ----------- | ----------------------------------------- |
| Category    | Usability                                 |
| Subcategory | Workflow comprehension — release approval |
| Standard    | IEC 62366-1, ISO 14971, 21 CFR Part 11    |

## 3. Hazard Identification

### 3.1 Hazard Description

The release workflow requires multiple sequential steps (create release → add documents → collect signatures → publish). Users may skip steps, attempt to publish without required signatures, or abandon the workflow mid-process, resulting in incomplete or invalid releases that do not meet 21 CFR Part 11 requirements.

### 3.2 Foreseeable Sequence of Events

1. User initiates a new release and adds documents to the release scope
2. User is unclear on which signatures are still required before publishing
3. User attempts to publish the release prematurely or skips a required signer
4. System rejects the publish or, worse, an unsigned release reaches production
5. Regulatory audit reveals incomplete signature records for a released version

### 3.3 Affected Users

- QA Managers orchestrating release approvals
- Developer Users adding documents to releases
- Regulatory Specialists verifying release completeness

## 4. Risk Estimation

### Before Mitigation

| Factor         | Rating             | Justification                                                                            |
| -------------- | ------------------ | ---------------------------------------------------------------------------------------- |
| Severity       | 4 — Major          | Invalid release signatures violate 21 CFR Part 11; regulatory finding likely             |
| Probability    | 3 — Occasional     | Multi-step workflows are commonly abandoned or misordered without clear guidance         |
| Detectability  | 2 — Good           | Missing signatures are detectable during release review but may be missed under pressure |
| **Risk Level** | **High (RPN: 24)** | 4 × 3 × 2                                                                                |

### After Mitigation

| Factor            | Rating           | Justification                                                                  |
| ----------------- | ---------------- | ------------------------------------------------------------------------------ |
| Severity          | 4 — Major        | Regulatory severity remains inherently high                                    |
| Probability       | 1 — Remote       | Navigation guards prevent publishing without required signatures               |
| Detectability     | 1 — High         | Step indicators and blocked transitions make missing steps immediately visible |
| **Residual Risk** | **Low (RPN: 4)** | 4 × 1 × 1                                                                      |

## 5. Risk Mitigation

| #   | Mitigation Measure                                                  | Type   | Target ID | Status      |
| --- | ------------------------------------------------------------------- | ------ | --------- | ----------- |
| 1   | Routing/navigation guards preventing premature step transitions     | Design | SDD-008    | Implemented |
| 2   | Step progress indicator showing current position in release flow    | Design | SDD-008    | Implemented |
| 3   | Publish button disabled until all required signatures are collected | Design | SDD-008    | Implemented |

## 6. Verification of Mitigation

| Mitigation # | Verification Method               | Evidence                         | Result  |
| ------------ | --------------------------------- | -------------------------------- | ------- |
| 1            | E2E test — premature publish      | Navigation guard rejection tests | Pending |
| 2            | Usability evaluation              | Task completion rate ≥ 90%       | Pending |
| 3            | Unit test — button disabled state | Release publish guard tests      | Pending |

## 7. Residual Risk Acceptability

Residual risk is **acceptable**. Navigation guards enforce the correct sequence and prevent publishing without complete signatures. The step progress indicator provides clear orientation within the workflow at all times.

## 8. Traceability

| Direction | Link Type    | Target ID | Title                |
| --------- | ------------ | --------- | -------------------- |
| Up        | derives-from | UN-005    | Release Management   |
| Down      | mitigated-by | SDD-008    | Routing & Nav Guards |

## 9. Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-16 | —      | Initial draft |
