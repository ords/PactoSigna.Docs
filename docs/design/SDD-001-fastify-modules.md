---
id: SDD-001
title: Fastify Modules
status: Draft
links:
  source:
    - docs/architecture/SAD-003-module-boundaries.md
  related:
    - docs/architecture/SAD-003-module-boundaries.md
---

# SDD-001 Fastify Modules

## Purpose

Describe the module registration and layering pattern used by `servicesApi`.

## Design

- Each module registers routes via a controller function.
- Controller handles schema wiring and delegates to services.
- Service enforces authorization, invariants, and audit actions.
- Repository performs Firestore-specific operations.

## Route Organization

- Core business routes live under `/api/v2`.
- Integration-specific routes use dedicated prefixes where required.

## Quality Rules

- Use Zod schema validation at route boundaries.
- Keep transport concerns in controllers and domain concerns in services.
