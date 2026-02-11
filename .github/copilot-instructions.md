# Guardrails

- Read `project-spec/` before acting. Do not invent requirements or scope beyond what is specified — over-scoping is the primary failure mode.
- Choose the secure option by default. Prefer least privilege, private access, and short-lived credentials unless the spec explicitly permits otherwise — security debt compounds silently.
- Never log, print, or embed secrets or PII. Use correlation IDs; redact sensitive values — leaks are irreversible.
- Surface trade-offs and risks before committing to irreversible choices — one-way doors deserve scrutiny.
- Prefer boring, well-understood solutions over novel ones unless the project spec justifies the complexity — novelty is great, but has a maintenance cost.

`.github/instructions/*.md` may add path-scoped rules (language/framework specific).

# Source of Truth

`project-spec/` is authoritative for this project:

- `project-spec/project.md` — goals, stack, non-goals
- `project-spec/constraints.md` — security, privacy, networking
- `project-spec/interfaces.md` — API/auth contracts (signatures only)
- `project-spec/infrastructure.md` — cloud/IaC conventions
- `project-spec/decisions/yyyy-mm-dd/` — committed architectural decisions (date-organized)
- `project-spec/todos.md` — prioritized tasks and milestones

If `project-spec/` is missing or incomplete, ask targeted questions and/or propose minimal scaffolding.
