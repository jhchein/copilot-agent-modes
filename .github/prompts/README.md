# Cheat Sheet

- If you want to onboard a project → /bootstrap
- If you want more ideas → /exploration (custom agent: `exploration`)
- If you want better decisions → /thinker (custom agent: `thinker`)
- If you want shape & boundaries → /architect (custom agent: `architect`)
- If you want code → /execution (custom agent: `execution`)
- If you want truth → /documenter (custom agent: `documenter`)
- If you want root cause → /debugger (custom agent: `debugger`)
- If you want scenario evaluation → /evaluator (custom agent: `evaluator`)

Optional patterns are offered by `/bootstrap` when context suggests they'd be useful — not enabled by default:

- Writing Quality Pattern (docs-focused instruction + skill + optional prompt wiring)
- Evaluation Harness Pattern (persona-based review prompts and scenario files)
- Project-Spec Hygiene Pattern (`project-spec` maintenance instruction)

These modes are project-independent. Project-specific constraints and contracts live in `project-spec/`.
Path-scoped instruction files live in `.github/instructions/` — see `examples/instructions/` for reference.
Custom agent definitions live in `.github/agents/`.
