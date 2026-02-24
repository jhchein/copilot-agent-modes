# Scenario Review Pattern (Example)

This folder shows a generic stakeholder-scenario review pattern.

Use it when a project needs multiple role-based reviews (for example: platform owner, security lead, operations owner) before committing documentation or architecture direction.

## Pattern pieces

- `persona.template.md` — template for stakeholder context and priorities.
- `review.prompt.template.md` — template for a role-specific review prompt.
- `evaluator.agent.template.md` — reusable evaluator agent baseline.

Keep scoring dimensions in the scenario file (`Scoring dimensions`) so prompts can reference one authoritative rubric.

When writing quality is scored, keep one source of truth in `.github/instructions/writing.instructions.md` and have prompts/skills reference it.

## Adoption guidance

1. Define 2-4 personas in `project-spec/scenarios/` from real stakeholder responsibilities.
2. Create one review prompt per persona using `review.prompt.template.md`.
3. Keep dimensions specific and measurable.
4. Treat scores as decision input, not automatic acceptance criteria.
