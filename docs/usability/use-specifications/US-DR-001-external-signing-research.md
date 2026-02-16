# UX Research: Auditor Access & External Signing in Medical Device QMS

## Research Context

This research investigates whether PactoSigna needs an "Auditor" role within its member model, or whether auditor needs are better served through document export + external signing capabilities. It also evaluates the "Contract Management" feature proposal (Sample.QMS.ISO13485#10) and its relationship to auditor workflows.

**Key hypothesis**: Auditors do not need to be platform members. Their needs are fully served by (1) exported document packages and (2) a signing mechanism for external parties.

---

## 1. How Audits Actually Work

### Audit Types in Medical Device Regulation

| Audit Type                | Duration     | Auditor Profile           | Document Access Pattern                    |
| ------------------------- | ------------ | ------------------------- | ------------------------------------------ |
| **ISO 13485 Stage 1**     | ~1 day       | Notified body auditor     | Documentation-only review before on-site   |
| **ISO 13485 Stage 2**     | Several days | Notified body auditor     | Comprehensive: docs + controls + processes |
| **Surveillance**          | Varies       | Certification body        | Annual spot-check of ongoing compliance    |
| **Recertification**       | Multi-day    | Certification body        | Full re-evaluation every 3 years           |
| **FDA Inspection (QSIT)** | 3-6 days     | FDA investigator          | Top-down sampling of 4 subsystems          |
| **Internal Audit**        | 1-2 days     | Internal QA or consultant | Organization-defined scope                 |

### How Auditors Receive Documentation

The "Front Room / Back Room" pattern dominates audit practice:

- **Front room**: Curated space (physical or digital) where the auditor works. A host/scribe is always present. Documents are brought to the auditor on request.
- **Back room**: Subject matter experts prepare requested documents.
- **Critical rule**: Never give auditors free access to browse an archive. Always provide an index from which they select, and bring copies -- never originals.

This translates directly to digital: a curated export package (front room) backed by the full system with controlled access (back room). The QA team controls what the auditor sees.

### Document Delivery Methods (Current Practice)

| Method                            | When Used                              | Prevalence                |
| --------------------------------- | -------------------------------------- | ------------------------- |
| Screen sharing during video call  | Remote/hybrid audits                   | Very common post-pandemic |
| Temporary read-only login to eQMS | Remote audits with tech-savvy auditors | Common in enterprise      |
| PDF export packages               | All audit types                        | Universal fallback        |
| File sharing (SharePoint/Teams)   | Desktop audits, pre-audit submissions  | Common                    |
| Physical printouts                | On-site audits                         | Declining but not gone    |

### Key Regulatory Change: QMSR (Feb 2026)

The FDA's new Quality Management System Regulation (QMSR) incorporates ISO 13485:2016 by reference and **removes the audit exception** -- FDA investigators can now review internal audit reports and management review records. This means export packages need to be more comprehensive, but it does not change the access pattern (auditors still receive curated packages, not system access).

---

## 2. How Competing QMS Platforms Handle Auditor Access

### Industry Survey

| Platform            | Auditor Access Model                  | Account Required?                    | Key Approach                                                                  |
| ------------------- | ------------------------------------- | ------------------------------------ | ----------------------------------------------------------------------------- |
| **Greenlight Guru** | On-demand artifact access             | Not documented                       | "Access your audit artifacts and provide them to the auditor in a few clicks" |
| **Qualio**          | "Basic" user role (read-only)         | Yes (time-limited)                   | Tagged document sets with temporary accounts                                  |
| **MasterControl**   | Web-based portal for authorized users | Yes (collaboration portal)           | Auditors use system to conduct audit programs                                 |
| **Veeva Vault QMS** | Auto-provisioned external users       | Yes (auto-provisioned/deprovisioned) | Lifecycle-state-triggered access                                              |
| **QT9 QMS**         | Dedicated compliance portal           | Separate portal                      | eDHR/eDMR export with one click                                               |
| **OpenRegulatory**  | Export-focused                        | No                                   | "Create exactly the PDFs your auditors expect"                                |

### The Split

The industry has NOT converged on a single pattern. There are two schools:

1. **Portal access** (Qualio, MasterControl, Veeva): Give auditors temporary accounts in the system. Requires identity management, access expiry, and simplified views.
2. **Export packages** (Greenlight Guru, OpenRegulatory, QT9): Generate comprehensive audit-ready packages. Auditors work with documents outside the system.

**Critical insight**: The portal-access platforms are **enterprise-focused** (Veeva starts at $50K+/yr, MasterControl at $109/user/month). SMB-focused platforms lean toward export packages.

PactoSigna targets startups and SMBs. The export-package approach aligns better with the target market and avoids the complexity tax of managing external user identities.

---

## 3. Jobs-to-Be-Done Analysis

### External Auditor Jobs

| Job                                    | Core Need                   | Does it require system access?               |
| -------------------------------------- | --------------------------- | -------------------------------------------- |
| Review published documents and records | Curated document package    | **No** -- export package serves this         |
| Trace document relationships           | Traceability matrix         | **No** -- included in attestation PDF        |
| Verify signatures and approval chain   | Signature attestation       | **No** -- included in export                 |
| Review audit trail entries             | Audit log export            | **No** -- CSV/PDF export serves this         |
| Sign audit findings/report             | E-signature on their report | **No** -- external signing link serves this  |
| Maintain independence from auditee     | Separation from the system  | **Better served** by NOT being in the system |

### Internal QA Team Jobs (Audit-Related)

| Job                             | Core Need                               | Current Support                       |
| ------------------------------- | --------------------------------------- | ------------------------------------- |
| Prepare audit packages          | Curated export of published releases    | Already built (export worker)         |
| Send documents to auditors      | Delivery mechanism                      | Email with download link (built)      |
| Receive signed audit reports    | Capture auditor's signature on findings | NOT built -- this is the gap          |
| Track audit history             | Record of which releases were audited   | NOT built                             |
| Manage auditor access lifecycle | Invite/expire/revoke                    | NOT needed if auditors aren't members |

### The Gap

The **only job** that requires auditor interaction with PactoSigna is: **signing their audit report/findings**. Everything else is handled by export. And signing does not require an account -- it requires a secure signing link (the DocuSign/PandaDoc pattern).

---

## 4. "Contract Management" in ISO 13485 Context

### Warning: Terminology Conflict

In ISO 13485, "Contract Management" has a specific meaning under **Clause 4.1.5 (Outsourced Processes)** and **Clause 7.4 (Purchasing Controls)**. It refers to:

- Supplier/vendor quality agreements (QAAs)
- Written agreements for outsourced processes
- Controls proportionate to risk
- Right-to-audit clauses

Using "Contract Management" as a feature name in a QMS platform will confuse users who expect it to mean supplier quality agreements. This is the #1 naming risk.

### What the Feature Actually Does

The proposed feature (Sample.QMS.ISO13485#10) is:

1. Upload a document (PDF)
2. Designate signature/initials locations
3. Send to external parties for signature
4. 30-day retention before requiring download
5. Optional cloud storage integration

This is **external document signing** -- closer to DocuSign than to supplier management.

### Recommended Name

The feature should be called **"Signing Request"** (not "Contract Management"). Reasoning:

| Name Considered     | Problem                                                                      |
| ------------------- | ---------------------------------------------------------------------------- |
| Contract Management | Conflicts with ISO 13485 Clause 4.1.5/7.4 terminology                        |
| Document Signing    | Too generic; could be confused with release signatures                       |
| External Signatures | Describes the mechanism, not the user's intent                               |
| Attestation Portal  | Too formal; implies a separate system                                        |
| **Signing Request** | Mirrors "Pull Request" in the developer world; action-oriented; clear intent |

A **Signing Request** is created by an internal member, contains one or more documents, designates external signers, and tracks completion. The name communicates what it is without conflicting with regulatory terminology.

---

## 5. E-Signature for External Parties

### The Universal Pattern

Every major e-signature platform (DocuSign, PandaDoc, Adobe Sign, HelloSign) uses the same flow for external signers:

1. Sender creates a signing request with documents and designates recipients
2. Recipients receive an email with a secure, time-limited link
3. Recipients review documents in the browser -- **no account creation needed**
4. Recipients apply their signature (drawn, typed, or uploaded)
5. Platform captures: timestamp, IP address, identity verification, meaning
6. Completed document is sealed and audit trail is generated
7. All parties receive the completed signed document

### Part 11 Implications

When external parties sign without a controlled account, the system operates as an **open system** under 21 CFR Part 11. Open systems require additional controls:

- Document encryption (TLS + at-rest encryption)
- Digital signature standards (consider PKI)
- Identity verification (email verification at minimum; MFA for high-risk)
- Signature display must include: full name, date/time with timezone, and meaning

### What This Means for PactoSigna

The Signing Request feature must:

1. **Not require account creation** for external signers
2. **Verify identity** via email link + optional additional factors
3. **Capture Part 11 fields**: signer identity, timestamp, meaning, IP address
4. **Generate audit trail** for the complete signing ceremony
5. **Make the link time-limited** and single-use
6. **Encrypt documents** in transit and at rest

---

## 6. Auditor Independence Argument

An underappreciated benefit of keeping auditors OUT of the system: **independence**.

ISO 19011 (Guidelines for Auditing Management Systems) requires:

> "Auditors should be independent of the activity being audited wherever practicable."

When an auditor has a login to the QMS, they become part of the system's user base. Their activity creates audit log entries. Their account must be managed, secured, and governed. This creates a subtle entanglement that real-world audit best practice discourages.

By contrast, an auditor who receives an export package and signs their findings via a Signing Request maintains clear separation from the system under audit.

---

## Sources

### Regulatory & Standards

- FDA QMSR Final Rule (Feb 2026): Incorporates ISO 13485:2016 by reference
- 21 CFR Part 11: Electronic Records, Electronic Signatures
- ISO 19011:2018: Guidelines for Auditing Management Systems
- ISO 13485:2016: Clauses 4.1.5, 7.4 (Outsourced Processes, Purchasing)

### Platform Documentation

- Qualio: User Permissions, Managing Internal & External Audits
- MasterControl: External Audit Software
- Veeva: Vault QMS External Collaboration
- Greenlight Guru: System Architecture & Security
- OpenRegulatory: Formwork export capabilities

### Industry Best Practices

- FDA Guide to Inspections of Quality Systems (QSIT methodology)
- SimplerQMS: Remote Auditing Best Practices
- Greenlight Guru: Virtual Audits guide
- Johner Institute: Quality Assurance Agreements
