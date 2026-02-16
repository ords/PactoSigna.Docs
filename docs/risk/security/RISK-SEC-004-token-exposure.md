---
id: RISK-SEC-004
title: 'Token Exposure'
status: draft
links:
  - type: derives-from
    target: PRS-004
  - type: mitigated-by
    target: SOP-005
---

# Token Exposure

## 1. Purpose

This document assesses the risk of OAuth tokens, GitHub App installation tokens, or API credentials being exposed to unauthorized parties. Token exposure could grant an attacker access to connected GitHub repositories and their content, compromising the integrity of the document source of truth.

## 2. Risk Category

| Field       | Value                                             |
| ----------- | ------------------------------------------------- |
| Category    | Security                                          |
| Subcategory | Credential management — token security            |
| Standard    | ISO 14971, OWASP API Security, FDA 21 CFR Part 11 |

## 3. Hazard Identification

### 3.1 Hazard Description

GitHub OAuth tokens, installation tokens, or webhook secrets may be exposed through: (a) tokens inadvertently sent to the frontend client, (b) tokens logged in application logs or error messages, (c) tokens stored in insecure locations (e.g., Firestore without access controls), or (d) tokens leaked through server-side request forgery or error responses.

### 3.2 Foreseeable Sequence of Events

1. A code defect causes a token to be included in an API response, log output, or error message
2. An attacker or unauthorized user obtains the exposed token
3. The attacker uses the token to access GitHub repositories, read source code, or modify content
4. Modified content is synced into the QMS, corrupting document state
5. QMS integrity is compromised; audit trail does not reflect the true source of changes

### 3.3 Affected Users / Stakeholders

- The organization whose GitHub repositories are compromised
- All organization members relying on document integrity
- Patients affected by corrupted medical device documentation
- GitHub organization administrators responsible for access security

## 4. Risk Estimation

### Before Mitigation

| Factor         | Rating             | Justification                                                                                   |
| -------------- | ------------------ | ----------------------------------------------------------------------------------------------- |
| Severity       | 5 — Catastrophic   | Token exposure grants direct access to source repositories; enables silent content manipulation |
| Probability    | 2 — Unlikely       | Requires specific code defect; modern frameworks discourage token exposure patterns             |
| Detectability  | 4 — Low            | Token leakage in logs or responses may go undetected without specific monitoring                |
| **Risk Level** | **High (RPN: 40)** | 5 × 2 × 4                                                                                       |

### After Mitigation

| Factor            | Rating               | Justification                                                                                |
| ----------------- | -------------------- | -------------------------------------------------------------------------------------------- |
| Severity          | 5 — Catastrophic     | Severity of token exposure remains catastrophic                                              |
| Probability       | 1 — Remote           | Server-side only storage, no frontend exposure, and secure credential handling minimize risk |
| Detectability     | 2 — Good             | Code review practices and security testing can detect exposure patterns                      |
| **Residual Risk** | **Medium (RPN: 10)** | 5 × 1 × 2                                                                                    |

## 5. Risk Mitigation

| #   | Mitigation Measure                                                        | Type    | Target ID | Status      |
| --- | ------------------------------------------------------------------------- | ------- | --------- | ----------- |
| 1   | GitHub credentials stored server-side only, never sent to frontend client | Design  | PRS-004    | Implemented |
| 2   | Installation tokens stored securely in Firestore with access controls     | Design  | PRS-004    | Implemented |
| 3   | OAuth callback handled server-side; tokens never appear in client URLs    | Design  | PRS-004    | Implemented |
| 4   | Webhook secret stored as environment variable, not in source code         | Design  | PRS-004    | Implemented |
| 5   | Backend-only writes architecture prevents frontend credential access      | Design  | PRS-004    | Implemented |
| 6   | Code review checklist includes credential exposure verification           | Process | SOP-005   | Implemented |

## 6. Verification of Mitigation

| Mitigation # | Verification Method              | Evidence                              | Result  |
| ------------ | -------------------------------- | ------------------------------------- | ------- |
| 1            | Code review — API response audit | No tokens in client-facing responses  | Pending |
| 2            | Firestore security rule tests    | Token collection access control tests | Pending |
| 3            | E2E test — OAuth flow            | Callback token handling verification  | Pending |
| 4            | Deployment config review         | Environment variable configuration    | Pending |
| 5            | Architecture review              | ADR-008 compliance verification       | Pending |
| 6            | Process audit                    | Code review records                   | Pending |

## 7. Residual Risk Acceptability

Residual risk is **acceptable** with monitoring. The residual RPN of 10 reflects the inherently catastrophic severity of token exposure combined with the difficulty of detecting novel leakage vectors. The server-side-only architecture and backend-only writes (ADR-008) eliminate the primary exposure vector (frontend leakage). Regular code review with credential exposure verification provides ongoing protection. Periodic security testing (per RISK-SEC-001) will validate that no new exposure paths have been introduced.

## 8. Traceability

| Direction | Link Type    | Target ID    | Title                          |
| --------- | ------------ | ------------ | ------------------------------ |
| Up        | derives-from | PRS-004       | GitHub App Integration         |
| Related   | related-to   | RISK-SEC-001 | Penetration Testing Plan       |
| Down      | mitigated-by | SOP-005      | Software Development Lifecycle |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
