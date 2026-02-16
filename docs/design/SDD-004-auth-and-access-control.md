---
id: SDD-004
title: Auth and Access Control
status: Draft
links:
  source:
    - docs/architecture/SAD-003-module-boundaries.md
  related:
    - docs/design/SDD-001-fastify-modules.md
---

# SDD-004 Auth and Access Control

## Purpose

Specify authentication and authorization responsibilities across backend layers.

## Authentication

- Firebase ID tokens are verified in Fastify auth plugin.
- Decoded claims are projected into request user context.

## Authorization

- Service layer performs organization membership and ownership checks.
- Resource-level validation applies before mutation or privileged read operations.

## Access Model

- Role-aware behavior is organization-scoped.
- Compliance-sensitive operations require explicit subject/resource linkage.

## Outcome

Centralized policy checks in services provide consistent enforcement and auditability.
