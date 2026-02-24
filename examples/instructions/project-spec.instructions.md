---
name: Project Spec Maintenance
description: Keep project-spec concise, accurate, and decision-aligned
applyTo: "project-spec/**/*.md"
---

# Project Spec Maintenance Instructions

## Source of truth discipline

- Keep project-specific facts in `project-spec/` only.
- If a requirement is unknown, keep `_TBD_` and add it to `project-spec/todos.md`.
- Do not invent requirements to fill gaps.

## Decision consistency

- Reflect accepted decisions in relevant files.
- If a decision changes, update the decision file status and affected sections.
- Avoid duplicating contradictory guidance across files.

## Scope and clarity

- Keep entries concrete and implementation-relevant.
- Mark non-applicable sections as `N/A`.
- Prefer short bullets over long narrative text.
