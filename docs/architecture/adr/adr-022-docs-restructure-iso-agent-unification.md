# ADR-022: Documentation Restructure — ISO 13485 & Agent Unification

## Status

Accepted

## Date

2026-02-15

## Context

PactoSigna defines the document types, traceability model, and folder structure that customers must follow for ISO 13485 / IEC 62304 Class C compliance. Yet our own `docs/` folder used a completely different structure (`docs/requirements/epic-01-*.md`, `docs/architecture/hld-*.md`). An auditor reviewing our repo would flag this inconsistency immediately.

Additionally, agent definitions were duplicated across `.github/agents/` and `.claude/agents/` (24 identical files), and `CLAUDE.md` was 379 lines — too large for efficient context loading.

The gap analysis (GitHub #184) identified 21 gaps. The execution plan (GitHub #185) proposed a 5-phase approach that was refined through 4 rounds of team brainstorming.

## Decision

### 1. Flat ISO structure at `docs/` root

No `docs/device/` or `docs/qms/` nesting. PactoSigna's FOLDER_RULES expects flat paths like `docs/user-needs/`, not `docs/device/user-needs/`.

```
docs/
├── architecture/           # ARCH-NNN, SAD-NNN, ADRs (IEC 62304 §5.3)
├── design/                 # DD-NNN, DDD-NNN (IEC 62304 §5.4)
├── user-needs/             # UN-NNN (IEC 62304 §5.2)
├── product-requirements/   # PR-NNN (ISO 13485 §7.2)
├── software-requirements/  # SWR-NNN (IEC 62304 §5.2)
├── test/                   # TEST-NNN, TP-NNN, TC-NNN (IEC 62304 §5.7)
├── risk/                   # RISK-SW/SEC/SIT/HARM-NNN (ISO 14971)
├── usability/              # UR-NNN, TA-NNN, US-NNN (IEC 62366-1)
├── sops/                   # SOP-NNN (ISO 13485 §4.2.4)
├── policies/               # POL-NNN (ISO 13485 §4.2.3)
├── work-instructions/      # WI-NNN (ISO 13485 §4.2.4)
├── plans/                  # PLAN-NNN (IEC 62304 §5.1, §6, §8.1)
├── soup/                   # SOUP-NNN (IEC 62304 §8)
├── context/                # Agent-loadable operational modules
├── templates/              # Frontmatter templates per doc type
└── .internal/sprints/      # Ephemeral AI agent sprint plans (non-QMS)
```

### 2. Nearly everything has a regulatory home

The original proposal kept personas, UX, domain, testing, security, and guides as non-ISO artifacts. Analysis with specific clause references proved all map to regulatory deliverables:

| Original Location               | New Location                                   | ISO/IEC Reference                |
| ------------------------------- | ---------------------------------------------- | -------------------------------- |
| `docs/personas/`                | `docs/usability/use-specifications/US-UP-NNN`  | IEC 62366-1 §5.1 (user profiles) |
| `docs/ux/` journeys             | `docs/usability/task-analyses/TA-NNN`          | IEC 62366-1 §5.4                 |
| `docs/ux/` JTBD                 | `docs/usability/use-specifications/US-UN-NNN`  | IEC 62366-1 §5.1 (user needs)    |
| `docs/domain/` bounded contexts | `docs/architecture/SAD-NNN`                    | IEC 62304 §5.3                   |
| `docs/domain/` aggregates       | `docs/design/DDD-NNN`                          | IEC 62304 §5.4                   |
| `docs/testing/`                 | `docs/test/` + `docs/work-instructions/`       | IEC 62304 §5.7                   |
| `docs/security/`                | `docs/risk/security/RISK-SEC-NNN`              | ISO 14971 §5.5                   |
| `docs/guides/`                  | `docs/work-instructions/WI-NNN` + `docs/sops/` | ISO 13485 §4.2.4                 |
| `docs/requirements/epic-*.md`   | `docs/software-requirements/SWR-NNN`           | IEC 62304 §5.2                   |

### 3. `.claude/` is source of truth for agents

VS Code reads from `.claude/`. Agent files live in `.claude/agents/` and `.claude/skills/`. No symlinks — `.github/agents/` duplicates were removed.

### 4. CLAUDE.md slimmed to ~80 lines

Operational details extracted to `docs/context/` modules (backend-patterns, frontend-patterns, commands, compliance-checklist, tech-stack). CLAUDE.md retains only: git rules, project identity, monorepo table, quick commands, key conventions, workflow summary.

### 5. Format-as-we-move

Files move to regulatory homes first. Frontmatter and document IDs are added during the move, not as a separate blocking step.

### 6. Plans classified

14 timestamped plan files analyzed individually:

- 5 purely ephemeral → `docs/.internal/sprints/`
- 3 should be ADRs → extracted to `docs/architecture/adr/`
- 4 mixed → design decisions extracted, sprint portions archived
- 1 product requirements → `docs/product-requirements/` as PRS-xxx
- 1 quality record → `docs/test/` as TC-xxx

### 7. ISO lifecycle plans added

Formal plans per IEC 62304 and ISO 14971 added to `docs/plans/`:

- SMP-001: Software Maintenance Plan (IEC 62304 §6)
- CMP-001: Configuration Management Plan (IEC 62304 §8.1)

## Consequences

### Positive

- PactoSigna's own docs follow the exact structure we tell customers to use
- Enables dog-fooding: connect our repo to PactoSigna's sync engine (Phase 4)
- Every document has a regulatory clause justification
- Agent context loading is efficient (~50 lines per module vs. 379-line CLAUDE.md)
- Document IDs prevent collisions via `docs/document-registry.md`

### Negative

- Large migration PR (~170 new/moved files)
- Some document content is initially thin (placeholders to be enriched)
- Old links from external docs or bookmarks will break

### Risks

- **Scope creep**: Each SOP/risk doc is intentionally 1-2 pages. Depth added later via PRs.
- **Broken workflows during migration**: Old files kept until Phase 5 cleanup to provide transition time.
- **ID collisions**: Document registry created before content. All IDs registered before file creation.

## References

- GitHub #184 — Gap analysis
- GitHub #185 — Phased execution plan
- ISO 13485:2016 — Quality management systems
- IEC 62304:2006+A1:2015 — Medical device software lifecycle
- IEC 62366-1:2015 — Usability engineering
- ISO 14971:2019 — Risk management
