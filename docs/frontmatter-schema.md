---
id: META-001
title: Frontmatter Schema Reference
status: effective
links: []
---

# Frontmatter Schema Reference

This document defines the YAML frontmatter schema used across all PactoSigna QMS documents. Every markdown document in the `docs/` hierarchy **SHALL** include a frontmatter block conforming to this schema.

---

## 1. Schema Definition

```yaml
---
id: <string> # REQUIRED — Unique document identifier
title: <string> # REQUIRED — Human-readable document title
status: <enum> # REQUIRED — Document lifecycle status
links: # OPTIONAL — Array of traceability links
  - type: <enum> #   REQUIRED per link — Relationship type
    target: <string> #   REQUIRED per link — Target document ID
---
```

---

## 2. Field Definitions

### 2.1 `id` (Required)

The unique identifier for the document. Must follow the ID format convention for its document type (see §4).

- **Type**: `string`
- **Format**: `{PREFIX}-{NNN}` or `{PREFIX}-{category}-{NNN}` (varies by type)
- **Constraints**: Must be unique across the entire document registry
- **Immutable**: Once assigned, an ID must never be reused or reassigned

### 2.2 `title` (Required)

A concise, human-readable title describing the document's content.

- **Type**: `string`
- **Constraints**: Should be descriptive enough to identify the document without opening it
- **Convention**: Use title case

### 2.3 `status` (Required)

The lifecycle status of the document.

- **Type**: `enum`
- **Valid values**:

| Value       | Meaning                                                                         |
| ----------- | ------------------------------------------------------------------------------- |
| `draft`     | Document is being authored or revised. Not yet approved for use.                |
| `effective` | Document has been reviewed, approved, and is the current authoritative version. |
| `obsolete`  | Document has been superseded or withdrawn. Retained for traceability.           |

- **Transitions**:
  - `draft` → `effective` (via review and approval)
  - `effective` → `draft` (when revision is initiated)
  - `effective` → `obsolete` (when document is retired)
  - `obsolete` → _(terminal — no further transitions)_

### 2.4 `links` (Optional)

An array of traceability links to other documents. Used to build the traceability matrix required by ISO 13485.

- **Type**: `array` of `{ type: string, target: string }`
- **Default**: `[]` (empty array) if no links exist
- **Constraints**: Each entry must have both `type` and `target`

---

## 3. Link Types

The `links[].type` field defines the semantic relationship between the current document and the target document.

| Type             | Semantics                                                                        | Direction        | Example                          |
| ---------------- | -------------------------------------------------------------------------------- | ---------------- | -------------------------------- |
| `derives-from`   | This document is derived from the target. The target is a higher-level source.   | Current ← Target | SWR-001 derives-from PRS-001      |
| `verified-by`    | This document's requirements are verified by the target test document.           | Current → Target | SWR-001 verified-by TC-001       |
| `implemented-by` | This document's design or requirement is implemented by the target.              | Current → Target | SWR-001 implemented-by SDD-001    |
| `mitigated-by`   | This risk is mitigated by the target document (requirement, design, or control). | Current → Target | RISK-SW-001 mitigated-by SWR-005 |
| `refines`        | This document refines or provides more detail for the target.                    | Current → Target | SDD-001 refines HLD-001          |

### Link Directionality Convention

Links are declared in the **downstream** document pointing **upstream**:

```
UN-001  (no links — top of hierarchy)
  ↑
PRS-001  (links: derives-from → UN-001)
  ↑
SWR-001 (links: derives-from → PRS-001)
  ↑
SDD-001  (links: derives-from → SWR-001)
```

Verification and mitigation links point in the reverse direction (from requirement to evidence):

```
SWR-001 (links: verified-by → TC-001)
RISK-SW-001 (links: mitigated-by → SWR-005)
```

---

## 4. ID Format Conventions

Each document type has a prescribed ID format. The `{NNN}` placeholder represents a zero-padded three-digit sequence number.

### 4.1 Requirements Hierarchy

| Type                 | Prefix | Format      | Directory                     | Example   |
| -------------------- | ------ | ----------- | ----------------------------- | --------- |
| User Need            | `UN`   | `UN-{NNN}`  | `docs/user-needs/`            | `UN-001`  |
| Product Requirement  | `PR`   | `PR-{NNN}`  | `docs/product-requirements/`  | `PRS-001`  |
| Software Requirement | `SWR`  | `SWR-{NNN}` | `docs/software-requirements/` | `SWR-001` |

### 4.2 Architecture & Design

| Type                         | Prefix | Format       | Directory                | Example    |
| ---------------------------- | ------ | ------------ | ------------------------ | ---------- |
| High-Level Design            | `HLD`  | `HLD-{NNN}`  | `docs/architecture/`     | `HLD-001`  |
| Software Architecture        | `SAD`  | `SAD-{NNN}`  | `docs/architecture/`     | `SAD-001`  |
| Architecture (general)       | `ARCH` | `ARCH-{NNN}` | `docs/architecture/`     | `HLD-001` |
| Software Design Description  | `SDD`  | `SDD-{NNN}`  | `docs/design/`           | `SDD-001`  |
| Domain-Driven Design         | `DDD`  | `DDD-{NNN}`  | `docs/design/`           | `DDD-001`  |
| Detailed Design (general)    | `DD`   | `DD-{NNN}`   | `docs/design/`           | `SDD-001`   |
| Architecture Decision Record | `ADR`  | `adr-{NNN}`  | `docs/architecture/adr/` | `adr-001`  |

### 4.3 Process Documents

| Type                         | Prefix | Format      | Directory                 | Example   |
| ---------------------------- | ------ | ----------- | ------------------------- | --------- |
| Standard Operating Procedure | `SOP`  | `SOP-{NNN}` | `docs/sops/`              | `SOP-001` |
| Work Instruction             | `WI`   | `WI-{NNN}`  | `docs/work-instructions/` | `WI-001`  |
| Quality Policy               | `POL`  | `POL-{NNN}` | `docs/policies/`          | `POL-001` |

### 4.4 Risk Management

| Type           | Prefix      | Format            | Directory               | Example         |
| -------------- | ----------- | ----------------- | ----------------------- | --------------- |
| Software Risk  | `RISK-SW`   | `RISK-SW-{NNN}`   | `docs/risk/software/`   | `RISK-SW-001`   |
| Security Risk  | `RISK-SEC`  | `RISK-SEC-{NNN}`  | `docs/risk/security/`   | `RISK-SEC-001`  |
| Usability Risk | `RISK-USE`  | `RISK-USE-{NNN}`  | `docs/risk/usability/`  | `RISK-USE-001`  |
| Situation Risk | `RISK-SIT`  | `RISK-SIT-{NNN}`  | `docs/risk/situations/` | `HS-001`  |
| Harm           | `RISK-HARM` | `RISK-HARM-{NNN}` | `docs/risk/harms/`      | `HARM-001` |

### 4.5 Test Documents

| Type            | Prefix | Format       | Directory    | Example    |
| --------------- | ------ | ------------ | ------------ | ---------- |
| Test Plan       | `TP`   | `TP-{NNN}`   | `docs/test/` | `TP-001`   |
| Test Case / Run | `TC`   | `TC-{NNN}`   | `docs/test/` | `TC-001`   |
| Test Report     | `TEST` | `TEST-{NNN}` | `docs/test/` | `TC-001` |

### 4.6 Usability Engineering

| Type                                | Prefix  | Format        | Directory                            | Example     |
| ----------------------------------- | ------- | ------------- | ------------------------------------ | ----------- |
| Task Analysis                       | `TA`    | `TA-{NNN}`    | `docs/usability/task-analyses/`      | `TA-001`    |
| Use Specification — Profile         | `US-UP` | `US-UP-{NNN}` | `docs/usability/use-specifications/` | `US-UP-001` |
| Use Specification — Validation      | `US-UV` | `US-UV-{NNN}` | `docs/usability/use-specifications/` | `US-UV-001` |
| Use Specification — User Needs      | `US-UN` | `US-UN-{NNN}` | `docs/usability/use-specifications/` | `US-UN-001` |
| Use Specification — Design Research | `US-DR` | `US-DR-{NNN}` | `docs/usability/use-specifications/` | `US-DR-001` |

---

## 5. Examples

### 5.1 User Need

```yaml
---
id: UN-001
title: Release Audit Trail
status: draft
links: []
---
```

### 5.2 Software Requirement with Traceability

```yaml
---
id: SWR-001
title: Document Sync from GitHub
status: effective
links:
  - type: derives-from
    target: PRS-003
  - type: verified-by
    target: TC-002
---
```

### 5.3 Risk Document

```yaml
---
id: RISK-SEC-001
title: Penetration Testing Plan
status: draft
links:
  - type: mitigated-by
    target: SWR-010
---
```

### 5.4 Architecture Document

```yaml
---
id: HLD-001
title: System Context
status: draft
links:
  - type: derives-from
    target: PRS-001
---
```

### 5.5 Design Document with Multiple Links

```yaml
---
id: SDD-001
title: Fastify Modules
status: draft
links:
  - type: derives-from
    target: SAD-003
  - type: refines
    target: HLD-001
  - type: verified-by
    target: TP-001
---
```

---

## 6. Validation Rules

The following rules **SHALL** be enforced (manually or by tooling in future phases):

1. **Uniqueness**: Every `id` must be globally unique across the document registry
2. **Format compliance**: Every `id` must match the pattern for its document type (§4)
3. **Required fields**: `id`, `title`, and `status` must be present in every document
4. **Valid status**: `status` must be one of: `draft`, `effective`, `obsolete`
5. **Valid link types**: `links[].type` must be one of: `derives-from`, `verified-by`, `implemented-by`, `mitigated-by`, `refines`
6. **Target existence**: `links[].target` should reference an `id` that exists in the document registry
7. **No orphans**: Every document with `status: effective` should be reachable from at least one traceability chain
8. **File naming**: Document files should be named `{id}-{slug}.md` (e.g., `SWR-001-document-sync.md`)

---

## 7. Traceability Chain

The full ISO 13485 traceability chain supported by this schema:

```
User Needs (UN)
  ↓ derives-from
Product Requirements (PR)
  ↓ derives-from
Software Requirements (SWR)
  ↓ implemented-by          ↓ verified-by
Design Documents (SDD/DD)   Test Documents (TP/TC)
  ↓ refines
Architecture (HLD/SAD/ARCH)

Risk Documents (RISK-*) ——mitigated-by——→ Requirements / Design
```

This chain enables auditors to trace from any user need down to its implementation and verification evidence, fulfilling ISO 13485 §7.3.
