---
id: SOP-004
title: 'Risk Management'
status: draft
links:
  - SOP-003
  - SOP-009
---

# Risk Management

## 1. Purpose

Establish the risk management process for PactoSigna, ensuring systematic identification, analysis, evaluation, and control of risks throughout the product lifecycle, in compliance with ISO 13485 §7.1 and ISO 14971.

## 2. Scope

Applies to all software-related risks that could affect data integrity, regulatory compliance, patient safety (indirect — via QMS accuracy), or system availability. Covers the entire product lifecycle from design through post-market.

## 3. Definitions

| Term          | Definition                                                        |
| ------------- | ----------------------------------------------------------------- |
| Hazard        | A potential source of harm (e.g., data loss, unauthorized access) |
| Risk          | Combination of probability of occurrence and severity of harm     |
| Risk Matrix   | Severity × Probability grid used to classify risk levels          |
| Mitigation    | A design or process control that reduces risk                     |
| Residual Risk | Risk remaining after all mitigations are applied                  |
| Risk File     | Collection of all risk artifacts for a product, in `docs/risk/`   |

## 4. Responsibilities

| Role               | Responsibility                                                   |
| ------------------ | ---------------------------------------------------------------- |
| Quality Manager    | Maintains the risk management file, reviews risk acceptability   |
| Software Architect | Identifies technical hazards, designs risk controls              |
| Developer          | Implements risk controls, reports new hazards discovered in code |
| Product Manager    | Identifies use-related hazards from user needs and workflows     |

## 5. Procedure

### 5.1 Risk Planning

1. Maintain the risk management plan in `docs/risk/risk-management-plan.md`.
2. Define the risk acceptability matrix (severity levels 1–5, probability levels 1–5).
3. Review and update the plan at each management review (SOP-010).

### 5.2 Hazard Identification

1. Identify hazards during design reviews, brainstorming sessions, and code reviews.
2. Document each hazard in the risk register (`docs/risk/risk-register.md`).
3. Categorize hazards: data integrity, access control, availability, compliance, usability.

### 5.3 Risk Analysis and Evaluation

1. For each hazard, estimate severity (1–5) and probability (1–5).
2. Calculate risk level using the risk matrix: Low (1–4), Medium (5–9), High (10–25).
3. Record analysis in the risk register with rationale for estimates.
4. Risks rated High require mitigation before release; Medium risks require documented justification.

### 5.4 Risk Control

1. Implement risk controls in priority order: design elimination, protective measures, information for safety.
2. For each mitigation, record: control description, implementation reference (PR or commit), and verification method.
3. Link risk controls to software requirements (`SWR-xxx`) or architecture decisions (`ADR-xxx`).
4. Verify effectiveness of each control via testing (SOP-007).

### 5.5 Residual Risk Assessment

1. Re-evaluate risk after controls are implemented.
2. Document residual risk level in the risk register.
3. If residual risk remains Medium or High, escalate to Quality Manager for accept/reject decision.
4. Assess overall residual risk acceptability before each release.

### 5.6 Post-Release Monitoring

1. Monitor production audit logs and error reports for new hazards.
2. Review CAPA records (SOP-009) for risk implications.
3. Update the risk register when new information becomes available.

## 6. Records

| Record                | Retention       | Location               |
| --------------------- | --------------- | ---------------------- |
| Risk management plan  | Life of product | `docs/risk/`           |
| Risk register         | Life of product | `docs/risk/`           |
| Risk analysis reports | Life of product | `docs/risk/`           |
| Mitigation evidence   | Life of product | GitHub PRs and commits |

## 7. References

- ISO 13485:2016 §7.1 — Planning of Product Realization
- ISO 14971:2019 — Application of Risk Management to Medical Devices
- SOP-003 — Design Control
- SOP-009 — Corrective & Preventive Action (CAPA)

## Revision History

| Version | Date       | Author        | Changes       |
| ------- | ---------- | ------------- | ------------- |
| 0.1     | 2026-02-15 | PactoSigna AI | Initial draft |
