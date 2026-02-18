# Agent Skills Design

This document proposes skill coverage for the six custom agents in `.github/agents/`.
It follows VS Code Agent Skills guidance (`.github/skills/<skill-name>/SKILL.md`) and keeps reusable capabilities in skills while keeping role behavior in agent files.

## Principles

- Keep agent files focused on role and handoffs.
- Put reusable procedures/examples/scripts in skills.
- Prefer `user-invokable: false` for background skills that should auto-load.
- Use explicit manual invocation only for high-risk or noisy skills.

## Skill map by agent

### thinker

- **decision-framing** (auto-load): identify one-way/two-way decisions, assumptions, reversibility.
- **constraint-check** (auto-load): verify alignment against `project-spec/constraints.md`.

### exploration

- **option-generation** (manual + auto): generate 3-6 options with trade-offs.
- **non-compliance-tagging** (auto-load): flag options that violate constraints.

### architect

- **contract-shaping** (manual + auto): define interfaces/signatures and data shapes.
- **boundary-checklist** (auto-load): ensure boundaries/responsibilities are explicit.

### execution

- **minimal-change-delivery** (auto-load): keep diffs small, reversible, and scoped.
- **validation-playbook** (manual + auto): run repository-native validation commands.

### debugger

- **root-cause-analysis** (manual + auto): separate facts from inference; rank hypotheses.
- **minimal-fix-gate** (auto-load): require high-confidence, local, reversible fixes.

### documenter

- **fact-only-docs** (auto-load): write docs from code/spec/approved decisions only.
- **decision-log-format** (manual + auto): consistent decisions/open-questions format.

## Suggested initial skill rollout

Start with one skill that teaches a genuine capability the agent prompts cannot fully encode:

1. `root-cause-analysis` — structured diagnostic procedure (fact gathering → hypothesis ranking → targeted verification → conditional fix)

See `project-spec/design/skills-phase1-contract.md` for the full execution contract.

### Deferred skills

Three originally-proposed skills are **deferred until observed failures justify them**:

- `constraint-check` — agent prompts already enforce constraint awareness; no observed misses
- `minimal-change-delivery` — agent prompts already enforce scope discipline; no observed over-scoping
- `fact-only-docs` — agent prompts already enforce factual documentation; no observed fabrication

These are guardrails restating existing directives, not capabilities. They may be revisited if concrete, repeatable failures demonstrate the agent prompts are insufficient. The remaining 8 skills in the skill map above are also deferred — they are design ideas, not committed work.

## Proposed directory structure

```text
.github/
  skills/
    root-cause-analysis/
      SKILL.md
```

## Deferred items

- Add scripts/templates only when repeated usage appears in real tasks.
- Consider org-level skill sharing only after stable local adoption.
