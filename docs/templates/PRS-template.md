---
id: PRS-{NNN}
title: '{Brief description of the product requirement}'
status: draft
links:
  - type: derives-from
    target: UN-{NNN}
---

# {Title}

## Requirement Statement

> The system **SHALL** {functional or performance requirement statement}.

## Rationale

{Why this requirement exists. Reference the parent user need and any applicable regulatory clauses.}

## Classification

| Field    | Value                                            |
| -------- | ------------------------------------------------ |
| Priority | {Essential / Desirable / Nice-to-have}           |
| Category | {Functional / Performance / Safety / Regulatory} |
| Standard | {ISO 13485 clause, IEC 62304 clause, or N/A}     |

## Acceptance Criteria

1. {Verifiable criterion}
2. {Additional criterion}

## Traceability

| Direction | Link Type    | Target ID | Title                        |
| --------- | ------------ | --------- | ---------------------------- |
| Up        | derives-from | UN-{NNN}  | {User need title}            |
| Down      | refined-by   | SRS-{NNN} | {Software requirement title} |
| Down      | verified-by  | TP-{NNN}  | {Test plan title}            |

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | YYYY-MM-DD | {name} | Initial draft |
