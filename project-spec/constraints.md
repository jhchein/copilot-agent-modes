# Constraints

## Template integrity

- Template files must be self-contained — no external runtime dependencies or required VS Code extensions.
- Skills must follow the [agentskills.io](https://agentskills.io/specification) open standard for cross-platform portability.
- Skills and instructions that reference `project-spec/` paths are acceptable — this is a template coupling that adopters accept. Skills must handle the case where referenced files are TBD/empty.

## Context budget

- Constitutional guardrails (`copilot-instructions.md`) must remain minimal — under 500 tokens. Procedural enforcement belongs in skills, not guardrails.
- Each skill body should stay under 5,000 tokens (agentskills.io recommendation). Target <300 tokens for guardrail-type skills.
- The total context cost of all skills loaded in a single turn should stay under 10,000 tokens to leave room for the agent prompt, user context, and tool output.

## Complexity boundary

- The template ships five customization layers (guardrails, agents, prompts, skills, instructions). Adding a sixth layer requires an explicit decision record justifying why five are insufficient.
- Avoid meta-orchestration (agents that coordinate other agents) until repeated manual chaining demonstrates clear need. Premature orchestration is over-scoping.
- Each layer has a single responsibility. If a concern can be addressed in an existing layer, do not create a new one.

## Security

- Secrets handling: N/A — the template contains no secrets. Adopting repos manage their own secrets per their own policies.
- Skills must not include scripts that execute with elevated privileges or access external services without explicit user approval via VS Code's terminal tool controls.

## Privacy & Data Handling

- PII policy: Template files must not contain PII. `project-spec/` may reference secret names or locations but must never contain secret values.
- Skills must not log, cache, or transmit user code or conversation content beyond the model context window.
