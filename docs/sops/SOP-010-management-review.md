---
id: SOP-010
title: 'Management Review'
status: draft
links:
  - SOP-004
  - SOP-008
  - SOP-009
---

# Management Review

## 1. Purpose

Define the management review process for PactoSigna's QMS, ensuring that the quality management system is periodically evaluated for suitability, adequacy, and effectiveness, in compliance with ISO 13485 §5.6.

## 2. Scope

Applies to the management-level review of QMS performance, conducted at regular intervals. Covers all team members in leadership or quality roles. Designed to be lightweight and practical for a small (3–5 person) AI-augmented team.

## 3. Definitions

| Term              | Definition                                            |
| ----------------- | ----------------------------------------------------- |
| Management Review | Periodic evaluation of QMS performance by management  |
| Review Input      | Data and metrics provided for management to evaluate  |
| Review Output     | Decisions and actions resulting from the review       |
| QMS Metrics       | Quantitative indicators of quality system performance |

## 4. Responsibilities

| Role             | Responsibility                                               |
| ---------------- | ------------------------------------------------------------ |
| Quality Manager  | Prepares review inputs, schedules review, records minutes    |
| Team Lead        | Participates in review, provides development metrics         |
| All Team Members | Provide input on process effectiveness and improvement ideas |

## 5. Procedure

### 5.1 Review Frequency

1. Conduct management reviews quarterly (every 3 months).
2. Additional ad-hoc reviews may be called after significant incidents, audit findings, or regulatory changes.
3. Schedule the review date at the end of each review for the next quarter.

### 5.2 Review Inputs

The Quality Manager prepares the following inputs before each review:

1. **CAPA Status** — Open/closed CAPA issues, effectiveness review results (from SOP-009).
2. **Audit Results** — Findings from internal audits or external assessments.
3. **Process Metrics** — Release frequency, CI pass rate, average PR review time, test coverage trends.
4. **Risk Status** — Current risk register summary, any new high-risk items (from SOP-004).
5. **Training Compliance** — Training completion rates, overdue tasks (from SOP-008).
6. **Customer Feedback** — User-reported issues, feature requests, complaint trends.
7. **Previous Review Actions** — Status of action items from the last management review.

### 5.3 Conducting the Review

1. The team meets (synchronously or asynchronously) to review all inputs.
2. For each input area, discuss: Is performance acceptable? Are trends positive? Are there systemic issues?
3. Identify areas needing improvement and propose action items with owners and due dates.

### 5.4 Review Outputs

1. Document the following decisions:
   - Process improvements needed (may trigger SOP revisions via SOP-006)
   - Resource needs (staffing, tooling, infrastructure)
   - Risk acceptability decisions
   - QMS objective updates for the next quarter
2. Assign action items to specific team members with deadlines.
3. Create GitHub Issues for any action items requiring implementation.

### 5.5 Record Keeping

1. Record review minutes in `docs/management-reviews/` as a dated markdown file.
2. Minutes must include: date, attendees, inputs reviewed, decisions made, and action items.
3. Track action items to closure in subsequent reviews.

## 6. Records

| Record              | Retention       | Location                   |
| ------------------- | --------------- | -------------------------- |
| Review minutes      | Life of product | `docs/management-reviews/` |
| Action item issues  | Life of product | GitHub Issues              |
| QMS metrics reports | 3 years         | Review minutes             |

## 7. References

- ISO 13485:2016 §5.6 — Management Review
- ISO 13485:2016 §5.6.2 — Review Input
- ISO 13485:2016 §5.6.3 — Review Output
- SOP-004 — Risk Management
- SOP-008 — Training & Competency
- SOP-009 — Corrective & Preventive Action (CAPA)

## Revision History

| Version | Date       | Author        | Changes       |
| ------- | ---------- | ------------- | ------------- |
| 0.1     | 2026-02-15 | PactoSigna AI | Initial draft |
