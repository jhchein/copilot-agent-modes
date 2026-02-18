# Skills Phase 1 — Design Contract

Status: **revised — ready for execution**
Date: 2026-02-18 (revised: scope reduced from 4 skills to 1)

## Context

`project-spec/design/agent-skills-design.md` originally identified 4 baseline skills. After architecture review, 3 were identified as guardrails restating existing agent prompt directives without solving an observed problem. Phase 1 is reduced to the single skill that teaches a genuine capability: `root-cause-analysis`.

This document specifies exactly what execution should produce, based on research of the [Agent Skills specification](https://agentskills.io/specification) and [VS Code agent skills docs](https://code.visualstudio.com/docs/copilot/customization/agent-skills) (approved 2026-02-04).

## Scope decision

### What ships

- `root-cause-analysis` — a structured diagnostic procedure (fact gathering → hypothesis ranking → targeted verification → conditional fix) that the debugger agent's brief prompt cannot fully encode. This is a genuine skill: it teaches a multi-step workflow the model would not follow unprompted.

### What is deferred

Three proposed skills — `constraint-check`, `minimal-change-delivery`, `fact-only-docs` — are **deferred indefinitely**, not cancelled. Rationale:

1. **No observed failure mode.** The agent prompts already state the rules these skills would enforce. There is no evidence the model ignores those rules, so the skills would be preemptive solutions for hypothetical problems.
2. **Guardrails in skill clothing.** These don't teach new capabilities — they restate agent directives as numbered checklists. The skills mechanism is designed for capabilities, not guidelines. When a design needs a rationale section explaining why something fits a mechanism, it probably doesn't.
3. **The project's own guardrail applies.** `copilot-instructions.md` says "over-scoping is the primary failure mode." Shipping 3 skills that solve no observed problem is over-scoping.

**Reactivation trigger:** If a concrete, repeatable failure appears (e.g., "the model consistently over-scopes changes in execution mode" or "documentation repeatedly contains invented claims"), design a skill to address that specific failure, informed by real evidence.

## Key research findings

### Discovery model (no explicit wiring)

Skills are **not referenced from agent frontmatter**. There is no `skills:` key on `.agent.md` files. Instead, VS Code uses a 3-level progressive disclosure model:

1. **Level 1 — Discovery (~100 tokens):** The model always sees all skills' `name` + `description` from frontmatter. This is the activation signal.
2. **Level 2 — Instructions (<5000 tokens recommended):** When the model determines a skill is relevant to the request, it loads the full `SKILL.md` body.
3. **Level 3 — Resources (on demand):** Files in `scripts/`, `references/`, `assets/` are loaded only when the body references them.

**Implication:** The `description` field is the primary activation mechanism. It must clearly state _what the skill does_ **and** _when to use it_.

### Invocation controls

| `user-invokable` | `disable-model-invocation` | Slash command | Auto-loaded by model | Use case        |
| ---------------- | -------------------------- | ------------- | -------------------- | --------------- |
| default (true)   | default (false)            | Yes           | Yes                  | General-purpose |
| default          | `true`                     | Yes           | No                   | On-demand only  |

### Spec constraints

- `name`: lowercase, hyphens only, 1-64 chars, must match directory name
- `description`: 1-1024 chars, must describe capability AND activation trigger
- Body: recommend <500 lines / <5000 tokens
- Directory name === `name` field (enforced; mismatch = skill not loaded)

### No agent file changes needed

Since skills are auto-discovered (not wired), Phase 1 requires **only new files** — no edits to existing `.agent.md` files.

## Directory structure

```text
.github/
  skills/
    root-cause-analysis/
      SKILL.md
```

No `scripts/`, `references/`, or `assets/` directories. This is a procedural skill, not script-based.

## Skill contract: `root-cause-analysis`

**Purpose:** A structured diagnostic procedure the model follows when investigating failures. It teaches a multi-step workflow (fact gathering → hypothesis ranking → targeted verification → conditional fix) that the debugger agent's brief prompt cannot fully encode.

**Invocation model:**

- `user-invokable: true` (default) — available as `/root-cause-analysis` slash command
- `disable-model-invocation: false` (default) — also auto-loads when debugging-related

**`argument-hint`:** `[error message or symptom description]`

**Description:** `Structured debugging procedure: collect facts, rank hypotheses by confidence, test with disconfirming evidence, propose fixes only at high confidence. Use when diagnosing errors, failures, or unexpected behavior.`

**When should the model load this?** When diagnosing errors, debugging failures, investigating unexpected behavior, or when the user explicitly requests root-cause analysis.

**Body contract (procedure):**

1. **Collect facts** — Gather error messages, logs, stack traces, reproduction steps, and environment details. Present facts in a `Facts` section. Distinguish:
   - **Observed:** directly seen in output/logs
   - **Reported:** stated by user but not independently verified
   - **Assumed:** inferred from context (mark explicitly)
2. **Identify unknowns** — What information is missing? What would narrow the search? List in an `Unknowns` section. If critical unknowns exist, ask before hypothesizing.
3. **Generate hypotheses** — Produce 2-5 ranked hypotheses in a `Hypotheses` table:

   | #   | Hypothesis | Supporting evidence | Disconfirming test | Confidence |
   | --- | ---------- | ------------------- | ------------------ | ---------- |

   Each hypothesis must be falsifiable. If it can't be tested, it's speculation, not a hypothesis.

4. **Test hypotheses** — Start with the highest-confidence hypothesis. Seek **disconfirming** evidence first (not confirming). Update the table with test results.
5. **Propose fix only when ALL conditions are met:**
   - Root cause identified with high confidence
   - Fix scope is local (does not require architectural changes)
   - Fix is reversible
   - Fix is directly tied to a stated, tested hypothesis
6. **If no hypothesis reaches high confidence:** Report findings, updated hypothesis table, and recommend specific next diagnostic steps. Do not guess a fix.

**Target size:** ~35-45 lines body

## Execution checklist

1. Create `.github/skills/root-cause-analysis/` directory
2. Create `SKILL.md` with frontmatter (`name`, `description`, `argument-hint`) and body per contract above
3. Keep body under 45 lines
4. No agent file edits required
5. Update `project-spec/todos.md` to mark P1 item complete

## Deferred decisions

- **Guardrail-type skills** (`constraint-check`, `minimal-change-delivery`, `fact-only-docs`) — deferred until an observed failure mode justifies them
- **Tool allowlists in agent frontmatter** — separate design pass (Phase 2)
- **`allowed-tools` in skill frontmatter** — agentskills.io supports this experimentally; not needed for this skill
- **Additional skills** — only when a specific capability gap is identified through use

## Evaluation criteria

After deployment, assess `root-cause-analysis` over 2-4 weeks:

| Signal                   | Healthy                                                         | Unhealthy → action                                            |
| ------------------------ | --------------------------------------------------------------- | ------------------------------------------------------------- |
| **Activation frequency** | Fires on debugging tasks, not on unrelated prompts              | Fires on non-debugging tasks → narrow the description         |
| **Procedural value**     | Model follows the 6-step procedure, producing structured output | Model ignores the procedure → rewrite the body                |
| **Context cost**         | <300 tokens body loaded                                         | Body exceeds budget → split into body + reference file        |
| **User invocation**      | `/root-cause-analysis` used when debugging complex issues       | Never invoked manually → reconsider whether it's discoverable |
