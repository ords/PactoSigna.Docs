---
id: SAD-003
title: Module Boundaries
status: Draft
links:
  related:
    - docs/design/SDD-001-fastify-modules.md
    - docs/design/SDD-007-feature-module-structure.md
---

# SAD-003 Module Boundaries

## Purpose

Define package and module responsibilities to prevent boundary leakage.

## Monorepo Boundaries

- `apps/web`: presentation and workflow composition.
- `apps/services`: HTTP API orchestration and domain service execution.
- `apps/functions`: asynchronous/scheduled execution units.
- `packages/data`: persistence and repository implementations.
- `packages/contracts`: request/response DTO schemas.
- `packages/shared`: reusable validators/predicates/calculations/constants.

## Boundary Rules

- Frontend does not import from `packages/data`.
- DTO ownership remains in `packages/contracts`.
- Business invariants live in service/domain layers, not controllers.

## Dependency Direction

UI and backend both depend on contracts/shared; persistence remains backend-only.

## Compliance Consideration

Boundary discipline supports audit-ready mutation paths and predictable ownership checks.
