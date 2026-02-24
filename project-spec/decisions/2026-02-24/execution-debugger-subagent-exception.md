# Execution → debugger subagent exception

- **Date**: 2026-02-24
- **Status**: accepted
- **Context**: The repo's default delegation model is handoffs (user-controlled transitions). The execution agent also declares `agents: ["debugger"]`, making the debugger available as a subagent — an agent-controlled delegation where the execution agent can invoke the debugger autonomously within a single turn. This was flagged as a contradiction with the handoff-only model documented in the README and `project-spec/constraints.md`.
- **Decision**:
  1. **Keep `agents: ["debugger"]` on the execution agent as a deliberate, recorded exception** to the handoff-default model.
  2. **Justification**: Error diagnosis during implementation is the highest-frequency delegation in the workflow. Requiring a manual handoff for every error interrupts execution flow for a transition that is both predictable and low-risk — the debugger diagnoses but does not modify code.
  3. **Subagent and handoff coexist for this pair.** The handoff (with `send: true`) remains for cases where the user wants to explicitly switch context. The subagent declaration allows the execution agent to invoke diagnosis inline when it encounters errors autonomously (e.g., during coding agent or background execution). These are different mechanisms with different context effects — handoff transfers the conversation; subagent runs within it.
  4. **This is the only approved subagent declaration** until repeated manual chaining demonstrates need for another.
  5. **Criteria for future subagent exceptions**: the delegation must be (a) high-frequency, (b) non-destructive (target agent does not make irreversible changes), and (c) same-direction (does not create circular delegation).
- **Alternatives considered**:
  - _Remove `agents: ["debugger"]` and rely solely on the handoff_: Rejected — loses autonomous diagnosis capability during coding agent and background execution, where no user is present to click a handoff button.
  - _Allow subagents generally_: Rejected — the handoff-first model provides better user visibility and control for most transitions. Generalizing subagents without evidence of need violates the complexity boundary constraint.
- **Consequences**: The README delegation model section must note the exception. No other agents gain subagent declarations without a new decision record.
- **Reversibility**: Two-way door. Removing `agents: ["debugger"]` from execution.agent.md restores the pure handoff model with no side effects.
