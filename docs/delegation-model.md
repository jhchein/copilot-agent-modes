# Delegation Model

This repo uses **handoffs** as its primary delegation pattern between agents.

## Handoffs

Handoffs are user-controlled transitions. After a chat response, a button appears that lets you switch to the next agent with relevant context. You review and approve every transition — you are the orchestrator.

### Handoff patterns

- **thinker → exploration**: broaden possibilities before narrowing.
- **exploration → architect**: collapse options into one coherent structure.
- **architect → execution**: implement only after boundaries and contracts are explicit.
- **execution → debugger**: switch to diagnosis-first mode when uncertainty appears.
- **execution → documenter**: record outcomes and decisions once work lands.
- **debugger → execution**: apply a minimal fix only after root-cause confidence is high.
- **evaluator → execution**: rework blocking findings with minimal, reversible edits.
- **evaluator → thinker**: reframe unresolved risks as options and decision questions.

## Why not subagents (by default)

Subagents are agent-controlled delegations that happen within a single turn — the agent decides when to delegate, not the user. This repo avoids them by default for three reasons:

1. **Prompt files don't compose with subagents.** When agent A invokes agent B as a subagent, only B's agent file runs — the prompt file is bypassed. Any task-specific framing in prompts is invisible to subagents.
2. **Visibility.** Handoffs are explicit; subagent delegations happen silently within a turn.
3. **Control clarity.** Mixing both models (some transitions user-controlled, some autonomous) creates ambiguity about who controls the workflow.

### One exception: execution → debugger

The execution agent declares debugger as a subagent because error diagnosis during implementation is high-frequency and non-destructive (the debugger diagnoses but does not modify code). This enables autonomous diagnosis during coding agent and background execution, where no user is present for a handoff. The handoff remains available for interactive use.

See `project-spec/decisions/2026-02-24/execution-debugger-subagent-exception.md` for the full rationale.

Future subagent exceptions require a decision record and must meet three criteria: high-frequency, non-destructive, and same-direction (no circular delegation).

## Design principles

- **Always-on instructions are a constitution**, not a playbook — short, behavioral, every token earns its place.
- **Agents are the behavioral layer** — each carries the complete role definition and works in all invocation contexts (dropdown, subagent, cloud).
- **Prompt files are the ergonomic layer** — slash commands that select the right agent and optionally add task-specific context.
- **Handoffs are the delegation layer** — user-controlled transitions between agents with suggested next steps.
- **Instruction files are path-scoped rules**, auto-applied by file pattern — generated per project by `/bootstrap`.
- **Project-spec is the only project-specific content** — everything else is reusable across projects.
