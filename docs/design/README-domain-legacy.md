# Domain Model Documentation

This directory contains the Domain-Driven Design documentation for PactoSigna.

---

## Overview

PactoSigna manages regulatory documentation for medical device companies using a multi-tenant architecture with GitHub as the document storage backend.

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         Identity & Access Context                        │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐                   │
│  │ Organization │──│    Member    │──│    Invite    │                   │
│  └──────────────┘  └──────────────┘  └──────────────┘                   │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    │ owns
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                    Repository & Owner Management Context                 │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐                   │
│  │  Repository  │──│     QMS      │  │    Device    │                   │
│  └──────────────┘  └──────────────┘  └──────────────┘                   │
└─────────────────────────────────────────────────────────────────────────┘
           │ contains                           │ owns
           ▼                                    ▼
┌──────────────────────────────┐  ┌────────────────────────────────────────┐
│   Document Management        │  │         Release Management              │
│  ┌──────────┐  ┌──────────┐  │  │  ┌─────────┐  ┌───────────┐            │
│  │ Document │──│   Link   │  │  │  │ Release │──│ Signature │            │
│  └──────────┘  └──────────┘  │  │  └─────────┘  └───────────┘            │
│  ┌──────────┐                │  │  ┌───────────────┐                      │
│  │   Risk   │                │  │  │ ChangeControl │                      │
│  └──────────┘                │  │  └───────────────┘                      │
└──────────────────────────────┘  └────────────────────────────────────────┘
                     │                              │
                     └──────────────┬───────────────┘
                                    ▼
                    ┌───────────────────────────────┐
                    │   Audit Context (Shared)      │
                    │  ┌─────────────────────────┐  │
                    │  │     AuditLogEntry       │  │
                    │  └─────────────────────────┘  │
                    └───────────────────────────────┘
```

---

## Bounded Contexts

| Context                       | Responsibility                                 | Aggregates                        |
| ----------------------------- | ---------------------------------------------- | --------------------------------- |
| Identity & Access             | Multi-tenancy, authentication, authorization   | Organization, Member, Invite      |
| Repository & Owner Management | GitHub integration, QMS/Device ownership       | Repository, QMS, Device           |
| Document Management           | Document indexing, traceability, risk          | Document, Link, Risk              |
| Release Management            | Approval workflows, signatures, change control | Release, Signature, ChangeControl |
| Audit (Shared Kernel)         | Immutable action logging                       | AuditLogEntry                     |

See [bounded-contexts.md](bounded-contexts.md) for detailed context documentation.

---

## Domain Documents

| Document                                      | Purpose                                  |
| --------------------------------------------- | ---------------------------------------- |
| [Bounded Contexts](bounded-contexts.md)       | Context definitions and boundaries       |
| [Aggregates & Entities](aggregates.md)        | Aggregate roots, entities, value objects |
| [Ubiquitous Language](ubiquitous-language.md) | Shared vocabulary glossary               |
| [Domain Events](domain-events.md)             | Event specifications (design phase)      |

---

## Code Locations

| Artifact         | Location                           |
| ---------------- | ---------------------------------- |
| Models (Types)   | `packages/data/src/models/`        |
| Repositories     | `packages/data/src/repositories/`  |
| API Contracts    | `packages/contracts/src/`          |
| REST Controllers | `apps/services/src/routes/api/v2/` |

---

## Key Architectural Decisions

- **ADR-008**: [Backend-Only Data Access](../architecture/adr-008-backend-only-firestore-writes.md) - All reads and writes go through the API
- **ADR-011**: [Bounded Context REST API Architecture](../architecture/adr-011-rest-api-migration.md) - API organized by bounded context
- **ADR-006**: [Metadata-Only Document Sync](../architecture/adr-006-metadata-only-document-sync.md) - Content fetched on demand from GitHub

---

## Domain Invariants Summary

| Aggregate     | Key Invariant                                  |
| ------------- | ---------------------------------------------- |
| Organization  | Must have at least one admin member            |
| Member        | Must belong to exactly one department          |
| Repository    | Must be owned by exactly one QMS or Device     |
| Device        | Must have safety class (A, B, or C)            |
| Document      | documentId unique within repository            |
| Release       | Cannot publish without all required signatures |
| Signature     | Immutable once created                         |
| AuditLogEntry | Immutable, server-timestamped                  |

See [aggregates.md](aggregates.md) for complete invariant documentation.
