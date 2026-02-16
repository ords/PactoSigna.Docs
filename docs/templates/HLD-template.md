---
id: '{HLD-NNN | SAD-NNN | ARCH-NNN}'
title: '{Architecture document title}'
status: draft
links:
  - type: derives-from
    target: '{SWR-NNN or PR-NNN}'
---

# {Title}

> **Document Type**: ISO 13485 Design Output
> **Applies To**: {System scope, e.g., "PactoSigna QMS Platform v1"}

## 1. Purpose

{What this architecture document covers and why it exists. Reference applicable IEC 62304 clauses.}

## 2. Scope

{Boundaries of this architecture — what is included and excluded.}

## 3. Context

{System context diagram or description. How this component fits into the broader system.}

## 4. Architecture Overview

{High-level architecture description. Include diagrams (Mermaid or ASCII) where helpful.}

```
{Diagram placeholder}
```

## 5. Key Design Decisions

{Reference relevant ADRs by number. Summarize the decisions that shaped this architecture.}

| ADR       | Decision         | Rationale         |
| --------- | ---------------- | ----------------- |
| ADR-{NNN} | {Decision title} | {Brief rationale} |

## 6. Component Description

### 6.1 {Component Name}

- **Responsibility**: {What it does}
- **Technology**: {Stack/framework}
- **Interfaces**: {APIs, events, or protocols}

### 6.2 {Component Name}

{Repeat for each significant component.}

## 7. Data Flow

{Describe the primary data flows through the system. Include sequence diagrams where helpful.}

## 8. Quality Attributes

| Attribute    | Requirement | How Achieved |
| ------------ | ----------- | ------------ |
| Performance  | {target}    | {mechanism}  |
| Security     | {target}    | {mechanism}  |
| Availability | {target}    | {mechanism}  |

## 9. Traceability

| Direction | Link Type      | Target ID | Title                        |
| --------- | -------------- | --------- | ---------------------------- |
| Up        | derives-from   | SRS-{NNN} | {Software requirement title} |
| Down      | implemented-by | SDD-{NNN} | {Design document title}      |

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | YYYY-MM-DD | {name} | Initial draft |
