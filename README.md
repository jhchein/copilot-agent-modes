# copilot-agent-modes

> Opinionated, project-independent agent modes for GitHub Copilot — copy into your repo, run `/bootstrap`, start building.

## Why

Copilot is capable but unstructured. Without guardrails, planning drifts into implementation, debugging becomes refactoring, and scope creeps silently. This template gives you distinct modes that keep different kinds of work separated — so each phase gets the right constraints, and handoffs between phases are explicit.

Copy the files, bootstrap your project context, start working.

## Quick start

1. Use this template (or copy `.github/` and `project-spec/` into your repo).
2. Open Copilot Chat and run `/bootstrap` — tell it about your project in the same message (e.g., "/bootstrap this project is a Node.js API that does X, the code lives in src/").
3. Answer the questions — it fills `project-spec/` and generates instruction files tailored to your codebase.
4. Use the modes as you work.

## Workflow

```text
/bootstrap       → onboard project, fill spec, generate instruction files
     ↓
/thinker         → plan, weigh options, evaluate decisions
/exploration     → generate 3–6 options with trade-offs
     ↓
/architect       → define structure, contracts, boundaries
     ↓
/execution       → implement agreed changes, minimal scope
     ↓
/debugger        → diagnose issues, fix only with proof
/documenter      → record what exists, nothing more

/evaluator       → score artifacts against scenario constraints (any stage)

Skills (auto-loaded when relevant):
  /root-cause-analysis → structured diagnostic procedure
```

## What's included

```text
.github/
  copilot-instructions.md          # Always-on guardrails (constitutional)
  agents/                          # Custom agents (workspace-level)
    thinker.agent.md
    exploration.agent.md
    architect.agent.md
    execution.agent.md
    debugger.agent.md
    documenter.agent.md
    evaluator.agent.md
  prompts/                         # Slash-command modes
    bootstrap.prompt.md            # /bootstrap — first-run onboarding
    thinker.prompt.md              # /thinker  — planning, options as real options
    exploration.prompt.md          # /exploration — divergent thinking, 3–6 options
    architect.prompt.md            # /architect — structure, contracts, boundaries
    execution.prompt.md            # /execution — implement agreed changes
    debugger.prompt.md             # /debugger — diagnose, minimal fix only
    documenter.prompt.md           # /documenter — record facts, nothing more
    evaluator.prompt.md            # /evaluator — scenario-based evaluation runner
    new-adopter.prompt.md          # /new-adopter — evaluate from adopter perspective
    template-contributor.prompt.md # /template-contributor — evaluate from maintainer perspective
  skills/                          # Reusable workflows (auto-discovered)
    root-cause-analysis/           # Structured diagnostic procedure
      SKILL.md
  instructions/                    # Path-scoped rules (empty — created per project)
project-spec/                      # Project-specific source of truth (TBD placeholders)
  scenarios/                       # Active evaluation scenarios for this repo
examples/instructions/             # Reference instruction files for inspiration
examples/scenarios/                # Evaluation harness pattern references
examples/skills/                   # Optional skill examples (e.g., writing-quality)
```

## How it works

Each mode has two files: an **agent** and a **prompt**.

- **Agents** (`.github/agents/*.agent.md`) carry the complete behavioral definition — role, rules, constraints, handoffs. They work in every context: the agent dropdown, subagent invocation, and cloud/background execution. Agents must be self-sufficient.
- **Prompts** (`.github/prompts/*.prompt.md`) are slash-command wrappers. Each prompt selects an agent via frontmatter and optionally adds task-specific context. Prompts are a convenience, not a safety layer.

**You control transitions.** When an agent finishes, it suggests a handoff — a button that switches to the next agent with context. You decide whether to follow. The flow typically runs: thinker → exploration → architect → execution → documenter, with debugger available when issues appear and evaluator available at any stage.

**Always-on guardrails** in `.github/copilot-instructions.md` apply to every agent, every turn. They're short and behavioral — the constitution, not the playbook.

**Project-spec is your project's source of truth.** Everything in `project-spec/` is specific to your project. Everything else in the template is reusable across repos.

For the full delegation rationale (why handoffs over subagents, exception criteria, design principles), see `docs/delegation-model.md`.

## Optional patterns

Beyond the base modes, the template includes optional patterns you can adopt incrementally:

- **Writing quality** — instruction file + skill for consistent, evidence-based documentation.
- **Evaluation harness** — persona-based scenario review using the evaluator agent.
- **Project-spec hygiene** — instruction file enforcing `_TBD_` conventions and fact-only content.

`/bootstrap` offers these based on your project context. See `examples/` for reference files and templates.

## Customizing

- **Modify any prompt** to match your working style — they're just Markdown files.
- **Add modes** by creating new `.prompt.md` files in `.github/prompts/`.
- **Instruction files** are generated per-project. See `examples/instructions/` for what good ones look like.
- **Project-spec** placeholders use `_TBD_` — fill them via `/bootstrap` or manually.

## License

[MIT](LICENSE)