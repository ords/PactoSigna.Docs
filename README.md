# PactoSigna Design History File

ISO 13485 / IEC 62304 / IEC 62366 / ISO 14971 compliant documentation for the PactoSigna QMS platform.

This repository is the **Design History File (DHF)** — the single source of truth for all regulatory and design documentation. It serves both human readers and AI agents equally.

## Structure

Every folder maps to an ISO/IEC regulatory deliverable:

| Folder | Standard | Prefix | Content |
|--------|----------|--------|---------|
| `docs/user-needs/` | ISO 13485 §7.2 | `UN-` | User needs and personas |
| `docs/product-requirements/` | ISO 13485 §7.3.3 | `PRS-` | Product-level requirements |
| `docs/software-requirements/` | IEC 62304 §5.2 | `SRS-` | Software requirements specifications |
| `docs/architecture/` | IEC 62304 §5.3 | `HLD-`, `SAD-` | High-level design and software architecture |
| `docs/architecture/adr/` | IEC 62304 §5.3 | `ADR-` | Architecture decision records |
| `docs/design/` | IEC 62304 §5.4 | `SDD-`, `DDD-` | Detailed design and domain-driven design |
| `docs/test/` | IEC 62304 §5.7 | `TC-`, `TP-` | Test cases and test plans |
| `docs/plans/` | IEC 62304 §5.1 | `SMP-`, `CMP-` | Lifecycle plans |
| `docs/risk/` | ISO 14971 | `RISK-*`, `HAZ-*`, `HS-`, `HARM-` | Risk management |
| `docs/usability/` | IEC 62366 | `US-`, `TA-`, `UE-`, `RISK-US-` | Usability engineering |
| `docs/policies/` | ISO 13485 §4.2.4b | `POL-` | Quality policies |
| `docs/sops/` | ISO 13485 §4.2.4c | `SOP-` | Standard operating procedures |
| `docs/work-instructions/` | ISO 13485 §4.2.4d | `WI-` | Work instructions |
| `docs/soup/` | IEC 62304 §8 | `SOUP-` | Software of unknown provenance |
| `docs/audits/` | ISO 13485 §8.2.4 | `AUD-` | Audit reports |

## Traceability

Documents link to each other via frontmatter `links:` arrays, forming traceable chains:

```
User Need (UN-001)
  → Product Requirement (PRS-001)
    → Software Requirement (SRS-101)
      → Detailed Design (SDD-001)
        → Test Case (TC-001)
```

## Related Repositories

- [PactoSigna.Experience.GitWeb](https://github.com/ords/PactoSigna.Experience.GitWeb) — Product source code
