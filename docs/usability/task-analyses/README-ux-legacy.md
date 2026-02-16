# UX Documentation

This directory contains user experience documentation for PactoSigna's core product flows. Each epic has two files:

- **user-journey.md** -- Step-by-step flows describing what the user sees, clicks, and the system response.
- **terminology.md** -- User-facing labels, their internal values, and definitions.

## Epics

### 1. [Document Management](./document-management/) (High Priority)

Browse, search, and inspect quality management documents synced from GitHub repositories. Includes document detail views with rendered markdown content, traceability links, changelog history, and the traceability matrix gap report.

### 2. [Release Management](./release-management/) (High Priority)

Create Releases that bundle changed documents for review, collect electronic Signatures from required departments (FDA 21 CFR Part 11 compliant), and publish with snapshot data. Covers the full Release lifecycle: Draft, In Review, Approved, Published.

### 3. [Risk Management](./risk-management/) (Medium Priority)

View inherent and residual risk heatmaps, filter by risk type (software, usability, security), review risk summary statistics, and identify gaps in risk documentation.

### 4. [Organization Setup](./organization-setup/) (Medium Priority)

Configure the Organization: general settings, regulatory frameworks, department management, team Member management, invitation workflows, GitHub Repository connections, and user profile management.

### 5. [Audit & Compliance](./audit-compliance/) (Low Priority)

Browse, filter, and export the audit log -- a complete chronological record of all actions performed within the Organization for FDA 21 CFR Part 11 compliance.

## Domain Terminology

This documentation uses the project's ubiquitous language consistently (see `docs/domain/ubiquitous-language.md`):

| Term         | Not                      |
| ------------ | ------------------------ |
| Organization | Company, Tenant, Account |
| Member       | User, Employee           |
| Release      | Version, Package         |
| Signature    | Sign-off, Approval       |
| Document     | File, Page               |
| Repository   | Repo, Source             |
