---
id: RISK-US-005
title: 'Signature Meaning Selection'
status: draft
links:
  - type: derives-from
    target: UN-006
  - type: mitigated-by
    target: SDD-004
---

# Signature Meaning Selection

## 1. Purpose

This document assesses the usability risk that signers select the wrong signature meaning (e.g., "reviewed" instead of "approved"), creating invalid electronic signatures per 21 CFR Part 11. This is a critical use-related risk per IEC 62366-1.

## 2. Risk Category

| Field       | Value                                             |
| ----------- | ------------------------------------------------- |
| Category    | Usability                                         |
| Subcategory | Selection accuracy — electronic signature meaning |
| Standard    | IEC 62366-1, ISO 14971, 21 CFR Part 11 §11.50     |

## 3. Hazard Identification

### 3.1 Hazard Description

21 CFR Part 11 §11.50 requires that electronic signatures include the meaning associated with the signature (e.g., "approval", "review", "authorship"). If a user selects an incorrect meaning, the signature record does not accurately reflect the signer's intent, potentially invalidating the entire signature and any dependent release or document approval.

### 3.2 Foreseeable Sequence of Events

1. User is prompted to sign a document or release with an electronic signature
2. User is presented with a dropdown of signature meanings (Approved, Reviewed, Authored)
3. User selects "Reviewed" when the workflow requires "Approved"
4. Signature is recorded with incorrect meaning; signatures are immutable once created
5. Release proceeds with technically invalid approval signature
6. Regulatory audit finds signature meaning does not match required workflow role

### 3.3 Affected Users

- QA Managers approving releases and documents
- Developer Users authoring or reviewing documents
- Regulatory Specialists validating signature compliance

## 4. Risk Estimation

### Before Mitigation

| Factor         | Rating               | Justification                                                          |
| -------------- | -------------------- | ---------------------------------------------------------------------- |
| Severity       | 4 — Major            | Invalid signature per Part 11 is a serious regulatory finding          |
| Probability    | 2 — Unlikely         | Dropdown selection errors are common; immutability prevents correction |
| Detectability  | 2 — Good             | Wrong meaning may be caught during release review but not guaranteed   |
| **Risk Level** | **Medium (RPN: 16)** | 4 × 2 × 2                                                              |

### After Mitigation

| Factor            | Rating           | Justification                                                           |
| ----------------- | ---------------- | ----------------------------------------------------------------------- |
| Severity          | 4 — Major        | Regulatory severity of invalid signatures unchanged                     |
| Probability       | 1 — Remote       | Context-filtered meanings and confirmation step prevent wrong selection |
| Detectability     | 1 — High         | Pre-submission confirmation displays selected meaning prominently       |
| **Residual Risk** | **Low (RPN: 4)** | 4 × 1 × 1                                                               |

## 5. Risk Mitigation

| #   | Mitigation Measure                                                     | Type   | Target ID | Status      |
| --- | ---------------------------------------------------------------------- | ------ | --------- | ----------- |
| 1   | Context-filtered signature meanings based on workflow role             | Design | SDD-004    | Implemented |
| 2   | Confirmation dialog displaying selected meaning and authentication     | Design | SDD-004    | Implemented |
| 3   | Signature meaning validated server-side against expected workflow role | Design | SDD-004    | Implemented |

## 6. Verification of Mitigation

| Mitigation # | Verification Method                | Evidence                           | Result  |
| ------------ | ---------------------------------- | ---------------------------------- | ------- |
| 1            | Unit test — meaning filtering      | Role-based meaning filter tests    | Pending |
| 2            | Usability evaluation               | Meaning selection accuracy ≥ 98%   | Pending |
| 3            | Unit test — server-side validation | Meaning validation rejection tests | Pending |

## 7. Residual Risk Acceptability

Residual risk is **acceptable**. Context-filtered meanings ensure users only see applicable options, the confirmation dialog makes the selection explicit, and server-side validation prevents recording a meaning that does not match the workflow context.

## 8. Traceability

| Direction | Link Type    | Target ID | Title                 |
| --------- | ------------ | --------- | --------------------- |
| Up        | derives-from | UN-006    | Electronic Signatures |
| Down      | mitigated-by | SDD-004    | Auth & Access Control |

## 9. Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-16 | —      | Initial draft |
