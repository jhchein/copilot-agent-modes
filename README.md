# copilot-agent-modes

> Opinionated, project-independent agent modes for GitHub Copilot — copy into your repo, run `/bootstrap`, start building.

## What is this?

A ready-to-use set of Copilot prompt files and custom agents that give you **structured agent modes** (think → explore → architect → execute → debug → document), **always-on guardrails**, and a **project-spec scaffolding** that gets filled interactively.

## Quick start

1. Use this template (or copy `.github/` and `project-spec/` into your repo).
2. Open Copilot Chat and run `/bootstrap`.
3. Answer the questions — it fills `project-spec/` and generates instruction files tailored to your codebase.
4. Use the modes as you work.

## Workflow

```text
/bootstrap       → onboard project, fill spec, generate instruction files
     ↓
/thinker         → run the thinker custom agent
/exploration     → run the exploration custom agent
     ↓
/architect       → run the architect custom agent
     ↓
/execution       → run the execution custom agent
     ↓
/debugger        → diagnose issues, fix only with proof
/documenter      → record what exists, nothing more

Skills (auto-loaded when relevant or via slash command):
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
  prompts/                         # Slash-command modes
    bootstrap.prompt.md            # /bootstrap — first-run onboarding
    thinker.prompt.md              # /thinker  — planning, options as real options
    exploration.prompt.md          # /exploration — divergent thinking, 3–6 options
    architect.prompt.md            # /architect — structure, contracts, boundaries
    execution.prompt.md            # /execution — implement agreed changes
    debugger.prompt.md             # /debugger — diagnose, minimal fix only
    documenter.prompt.md           # /documenter — record facts, nothing more
  skills/                          # Reusable workflows (auto-discovered)
    root-cause-analysis/           # Structured diagnostic procedure
      SKILL.md
  instructions/                    # Path-scoped rules (empty — created per project)
project-spec/                      # Project-specific source of truth (TBD placeholders)
examples/instructions/             # Reference instruction files for inspiration
```

## Prompt files vs custom agents

Each mode has two files: an **agent** (`.github/agents/<name>.agent.md`) and a **prompt** (`.github/prompts/<name>.prompt.md`). They have different responsibilities:

| Layer      | Responsibility                                                                     | When it runs                                                              |
| ---------- | ---------------------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| **Agent**  | Complete behavioral definition — role, rules, constraints, handoffs                | Always: direct selection, subagent invocation, cloud/background execution |
| **Prompt** | Ergonomic slash-command wrapper — selects the agent, may add task-specific context | Only when the user invokes the `/` command                                |

**Agents must be self-sufficient.** An agent should behave correctly when selected directly from the agents dropdown, without the corresponding prompt file. The prompt is a convenience, not a safety layer.

**Prompts select agents, not the other way around.** Each prompt uses `agent: <name>` in frontmatter to activate its agent. The prompt body adds task-specific framing (e.g., additional guidelines or context) on top of the agent's own instructions.

Keep always-on constraints in `.github/copilot-instructions.md`; keep project-specific truth in `project-spec/`.

## Delegation model: handoffs, not subagents

This repo uses **handoffs** as its delegation pattern. Handoffs are user-controlled transitions: after a chat response, a button appears that lets the user switch to the next agent with relevant context.

**Why handoffs:**

- The user reviews and approves every agent transition.
- Each transition is visible and steerable.
- The user is the orchestrator.

**Why not subagents:**

- Subagents are agent-controlled delegations that happen within a single turn — the agent decides when to delegate, not the user.
- Prompt files don't compose with subagents. When agent A invokes agent B as a subagent, only B's agent file runs — the prompt file is bypassed. This means any task-specific framing in prompts is invisible to subagents.
- Mixing both models (some transitions user-controlled, some autonomous) creates ambiguity about who controls the workflow.

The handoff pattern is deliberate, not a gap. A coordinator agent with subagent delegation may be added later, but only once repeated manual chaining demonstrates clear need (see `project-spec/constraints.md`).

## Handoff patterns

- **thinker → exploration**: broaden possibilities before narrowing.
- **exploration → architect**: collapse options into one coherent structure.
- **architect → execution**: implement only after boundaries and contracts are explicit.
- **execution → debugger**: switch to diagnosis-first mode when uncertainty appears.
- **execution → documenter**: record outcomes and decisions once work lands.
- **debugger → execution**: apply a minimal fix only after root-cause confidence is high.

## Design principles

- **Always-on instructions are a constitution**, not a playbook — short, behavioral, every token earns its place.
- **Agents are the behavioral layer** — each carries the complete role definition and works in all invocation contexts (dropdown, subagent, cloud).
- **Prompt files are the ergonomic layer** — slash commands that select the right agent and optionally add task-specific context.
- **Handoffs are the delegation layer** — user-controlled transitions between agents with suggested next steps.
- **Instruction files are path-scoped rules**, auto-applied by file pattern — generated per project by `/bootstrap`.
- **Project-spec is the only project-specific content** — everything else is reusable across projects.

## Customizing

- **Modify any prompt** to match your working style — they're just Markdown files.
- **Add modes** by creating new `.prompt.md` files in `.github/prompts/`.
- **Instruction files** are generated per-project. See `examples/instructions/` for what good ones look like.
- **Project-spec** placeholders use `_TBD_` — fill them via `/bootstrap` or manually.

## License

[MIT](LICENSE)
