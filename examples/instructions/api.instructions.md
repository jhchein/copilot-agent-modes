---
name: API Development
description: Request/response contracts, auth enforcement, and observability for API code
applyTo: "src/api/**/*.py"
---

# API Development Instructions

## Core Principles

- Prefer explicit request/response schemas and stable contracts.
- Enforce authentication/authorization on all non-public endpoints.
- Avoid logging sensitive data (PII/secrets). Use correlation IDs.

## Implementation Rules

- Initialize expensive clients in an application startup/lifecycle hook (framework-appropriate).
- Centralize client construction (factory or dependency injection) to avoid ad-hoc global state.
- Ensure errors are user-safe and observability-friendly (correlation IDs, structured logs).

## Configuration

- Document required env vars and defaults in `project-spec/project.md`.
- If the project uses “mock vs live” modes, define the exact flags in `project-spec/interfaces.md`.
