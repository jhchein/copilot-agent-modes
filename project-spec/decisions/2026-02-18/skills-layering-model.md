# Skills layering model — how skills relate to agents, prompts, and instructions

- **Date**: 2026-02-18 (revised: scope reduced from 4 skills to 1)
- **Status**: accepted (revised)
- **Context**: The repo is adding Agent Skills (`.github/skills/`) as a fourth customization layer alongside guardrails, agents/prompts, and instructions. The architecture work surfaced questions about how these layers interact: duplication with agent prompts, activation scope, and where the complexity boundary sits. A subsequent review found that 3 of 4 proposed skills were guardrails restating existing agent directives with no observed failure to motivate them — only `root-cause-analysis` teaches a genuine capability.
- **Decision**:
  1. **Skills are auto-discovered, not wired.** No `skills:` key in agent frontmatter. Skills activate via model-decided relevance matching based on the `description` field. This means agent files do not need edits when skills are added or removed.
  2. **Agent prompts define identity; skills provide procedures.** Agent prompts state _what to be_ (always loaded). Skills teach _how to do something the model wouldn't do unprompted_ (loaded on demand). Overlap between prompts and skills is only justified when the skill adds meaningful procedural structure.
  3. **Guardrail-type skills are deferred, not rejected.** The 3 proposed guardrail skills (`constraint-check`, `minimal-change-delivery`, `fact-only-docs`) were reconsidered. They add procedural checklists, but agent prompts already enforce the underlying rules and no observed failures justify the additional complexity. They should be created only when a specific, repeatable failure mode demonstrates the need.
  4. **Skill descriptions must be narrow.** Descriptions target specific user activities ("when diagnosing errors, failures, or unexpected behavior") to avoid over-activation. Descriptions that would match most prompts are a design smell.
  5. **Five layers is the complexity ceiling.** Guardrails, agents, prompts, skills, instructions. Adding a sixth requires an explicit decision record. Coordinator/orchestration agents are deferred until demonstrated need.
  6. **Ship only capability-type skills until evidence demands otherwise.** The skills mechanism is designed for capabilities, not guidelines. Guardrail skills will be revisited only when a concrete, repeatable failure shows agent prompts are insufficient.
- **Alternatives considered**:
  - _Ship all 4 skills as originally designed_: Rejected — 3 of 4 are solutions without observed problems, violating the project's own "over-scoping is the primary failure mode" guardrail.
  - _Use custom instructions instead of skills for guardrails_: Still available as an option if guardrail needs emerge.
  - _Wire skills into agent frontmatter_: Not possible — VS Code has no `skills:` key on agents. Skills discovery is model-driven by design.
  - _Trim agent prompts to defer to skills_: Rejected — agent prompts must stand alone because skills may not fire. Trimming would weaken the baseline.
- **Consequences**: One new skill directory (`.github/skills/root-cause-analysis/`). No changes to existing agent or prompt files. Activation quality must be monitored per evaluation criteria in `skills-phase1-contract.md`. Guardrail skills may be added later if failures justify them.
- **Reversibility**: Two-way door. Skills are just files — deleting a skill directory fully removes it with no side effects on agents or prompts.
