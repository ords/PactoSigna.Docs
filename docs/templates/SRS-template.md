---
id: SRS-{NNN}
title: '{Brief description of the software requirement}'
status: draft
links:
  - type: derives-from
    target: PRS-{NNN}
---

# {Title}

## Requirement

> The software **SHALL** {precise, testable software requirement}.

## Context

{Technical context explaining how this requirement maps to a product requirement. Reference IEC 62304 software development lifecycle clauses where applicable.}

## Classification

| Field           | Value                                                                 |
| --------------- | --------------------------------------------------------------------- |
| Safety Class    | {A / B / C per IEC 62304}                                             |
| Category        | {Functional / Interface / Data / Security / Performance}              |
| Bounded Context | {Identity & Access / Document Management / Release Management / etc.} |

## Detailed Specification

### Inputs

{Describe the inputs, triggers, or preconditions.}

### Processing

{Describe the processing logic or behavior.}

### Outputs

{Describe the expected outputs, state changes, or side effects.}

### Error Handling

{Describe expected error conditions and how they SHALL be handled.}

## Acceptance Criteria

1. **Given** {precondition}, **when** {action}, **then** {expected result}
2. {Additional criterion in Given/When/Then or declarative format}

## Traceability

| Direction | Link Type      | Target ID | Title                       |
| --------- | -------------- | --------- | --------------------------- |
| Up        | derives-from   | PRS-{NNN}  | {Product requirement title} |
| Down      | implemented-by | SDD-{NNN} | {Design document title}     |
| Down      | verified-by    | TC-{NNN}  | {Test case title}           |

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | YYYY-MM-DD | {name} | Initial draft |
