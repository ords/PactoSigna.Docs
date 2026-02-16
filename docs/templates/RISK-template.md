---
id: 'RISK-{category}-{NNN}'
title: '{Risk document title}'
status: draft
links:
  - type: mitigated-by
    target: '{SWR-NNN or SSDD-NNN}'
---

# {Title}

## 1. Purpose

{What risk this document assesses and why. Reference ISO 14971 and IEC 62304 where applicable.}

## 2. Risk Category

| Field       | Value                                                     |
| ----------- | --------------------------------------------------------- |
| Category    | {software / security / usability / situation / harm}      |
| Subcategory | {specific area, e.g., "authentication", "data integrity"} |
| Standard    | {ISO 14971, IEC 62304, FDA guidance, etc.}                |

## 3. Hazard Identification

### 3.1 Hazard Description

{Describe the hazard or hazardous situation.}

### 3.2 Foreseeable Sequence of Events

{How the hazard could lead to harm.}

### 3.3 Affected Users / Stakeholders

- {User group or stakeholder}

## 4. Risk Estimation

### Before Mitigation

| Factor         | Rating                                   | Justification     |
| -------------- | ---------------------------------------- | ----------------- |
| Severity       | {1-5 or qualitative}                     | {why}             |
| Probability    | {1-5 or qualitative}                     | {why}             |
| **Risk Level** | **{Low / Medium / High / Unacceptable}** | {per risk matrix} |

### After Mitigation

| Factor            | Rating                    | Justification     |
| ----------------- | ------------------------- | ----------------- |
| Severity          | {1-5 or qualitative}      | {why}             |
| Probability       | {1-5 or qualitative}      | {why}             |
| **Residual Risk** | **{Low / Medium / High}** | {per risk matrix} |

## 5. Risk Mitigation

| #   | Mitigation Measure | Type                             | Target ID | Status                             |
| --- | ------------------ | -------------------------------- | --------- | ---------------------------------- |
| 1   | {description}      | {Design / Process / Information} | {SWR-NNN} | {Planned / Implemented / Verified} |
| 2   | {description}      | {type}                           | {target}  | {status}                           |

## 6. Verification of Mitigation

{How each mitigation is verified as effective. Reference test cases or design reviews.}

| Mitigation # | Verification Method | Evidence                  | Result           |
| ------------ | ------------------- | ------------------------- | ---------------- |
| 1            | {method}            | {TC-NNN or review record} | {Pass / Pending} |

## 7. Residual Risk Acceptability

{Statement on whether residual risk is acceptable per the risk management plan. If not, what further action is required.}

## 8. Traceability

| Direction | Link Type    | Target ID | Title               |
| --------- | ------------ | --------- | ------------------- |
| Down      | mitigated-by | SRS-{NNN} | {Requirement title} |
| Down      | verified-by  | TC-{NNN}  | {Test case title}   |

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | YYYY-MM-DD | {name} | Initial draft |
