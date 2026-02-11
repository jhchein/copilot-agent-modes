---
name: Ingestion Pipelines
description: Data ingestion conventions — storage layout, connector patterns, network security
applyTo: "src/ingestion/**/*.py"
---

# Ingestion Development Instructions

## Data Storage

Define the canonical path structure (or bucket/container layout) in `project-spec/infrastructure.md`.

## Connector Patterns

- Prefer workload/managed identity over long-lived secrets where the platform supports it.
- Define delta/update strategy explicitly (overwrite vs versioning) in `project-spec/decisions/`.
- Normalize content into a small set of canonical formats (e.g., Markdown/HTML/text).

## Network Security

- Follow the networking constraints defined in `project-spec/constraints.md`.
