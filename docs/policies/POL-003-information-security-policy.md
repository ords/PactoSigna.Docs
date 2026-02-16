---
id: POL-003
title: 'Information Security Policy'
status: draft
links:
  - type: implemented-by
    target: SOP-005
---

# Information Security Policy

## 1. Purpose

This policy establishes PactoSigna's commitment to protecting QMS data confidentiality, integrity, and availability. As a system that manages electronic records with regulatory significance, information security is essential for FDA 21 CFR Part 11 compliance and customer trust.

## 2. Scope

This policy applies to all PactoSigna systems, data, and personnel. It covers the PactoSigna web application, Cloud Functions, Firestore database, GitHub integrations, and all supporting infrastructure. It applies to all environments: production, staging, and development.

## 3. Policy Statement

> PactoSigna shall protect the confidentiality, integrity, and availability of all QMS data entrusted to the platform. Security controls shall meet or exceed the requirements of FDA 21 CFR Part 11, ISO 13485 §4.2.4, and industry best practices for cloud-based SaaS applications.

## 4. Definitions

| Term              | Definition                                                                      |
| ----------------- | ------------------------------------------------------------------------------- |
| Electronic Record | Any QMS document, audit log, signature, or metadata stored in PactoSigna        |
| Confidentiality   | Information is accessible only to authorized individuals                        |
| Integrity         | Information is accurate, complete, and protected from unauthorized modification |
| Availability      | Information is accessible when needed by authorized users                       |

## 5. Requirements

### 5.1 Access Control

- All system access shall require authentication via Firebase Authentication
- Role-based access control (Owner, Admin, Member) shall enforce least-privilege principles
- All Firestore mutations shall be processed through Cloud Functions only (no direct client writes)
- Session tokens shall expire after a defined inactivity period

### 5.2 Data Protection

- Electronic records shall be immutable where required by regulation (audit logs, signatures)
- Data at rest shall be encrypted using Google Cloud Platform default encryption
- Data in transit shall be encrypted using TLS 1.2 or higher
- GitHub credentials (OAuth tokens, installation tokens) shall be stored server-side only

### 5.3 Part 11 Compliance

- Electronic signatures shall require re-authentication at time of signing
- Audit trails shall use server-generated timestamps and be tamper-evident
- Each user shall have a unique identity; shared accounts are prohibited
- Signature records shall be linked to their respective electronic records and shall not be transferable

### 5.4 Incident Response

- Security incidents shall be reported and investigated promptly
- Affected customers shall be notified in accordance with applicable data breach regulations
- Incident root causes shall be addressed through the CAPA process (SOP-009)

## 6. Roles and Responsibilities

| Role               | Responsibility                                                         |
| ------------------ | ---------------------------------------------------------------------- |
| Leadership         | Approve security policy, allocate security resources                   |
| Software Engineers | Implement security controls, follow secure coding practices            |
| QA Manager         | Verify security controls through testing and audits                    |
| All Personnel      | Report security incidents, protect credentials, follow access policies |

## 7. Compliance

Compliance with this policy is monitored through:

- Periodic security assessments and penetration testing (RISK-SEC-001)
- Code review with security checklist
- Firestore security rule testing
- Management review of security metrics (SOP-010)

Non-compliance shall be treated as a security incident and addressed through the CAPA process (SOP-009).

## 8. Related Documents

| ID           | Title                            | Relationship           |
| ------------ | -------------------------------- | ---------------------- |
| SOP-005      | Software Development Lifecycle   | Implements this policy |
| SOP-009      | Corrective and Preventive Action | Incident response      |
| RISK-SEC-001 | Penetration Testing Plan         | Security assessment    |
| RISK-SEC-002 | Authentication Bypass            | Risk analysis          |
| RISK-SEC-004 | Token Exposure                   | Risk analysis          |

## 9. Regulatory References

- ISO 13485:2016 §4.2.4 — Control of documents (electronic records)
- FDA 21 CFR Part 11 §11.10 — Controls for closed systems
- FDA 21 CFR Part 11 §11.30 — Controls for open systems
- FDA 21 CFR Part 11 §11.50 — Signature manifestations
- FDA 21 CFR Part 11 §11.100 — General requirements for electronic signatures

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
