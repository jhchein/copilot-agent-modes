# Guardrail Pattern (Example)

This folder shows an optional guardrail pattern for projects where planning agents need stronger adversarial critique before they commit to recommendations.

Use it when a planning or architecture agent produces plausible recommendations that still contain unexamined assumptions, missed risks, or overlooked alternatives.

## Pattern pieces

- `challenger.agent.template.md` — reusable adversarial sub-agent baseline
- `prompt-wiring-examples.md` — prompt rules, workflow sequence, and visible output contract that make the challenger pattern reliable

The agent file alone is not sufficient. The pattern works as a bundle:

1. Add the challenger as a sub-agent on the planning agent.
2. Add explicit prompt rules forbidding commitment without challenger review.
3. Add a workflow step that invokes the challenger before the recommendation is formed.
4. Add a visible output section so skipped challenger runs are immediately detectable.

## Model selection for guardrail roles

The primary agent and its guardrail sub-agents benefit from contrasting behavioral profiles.

| Role                   | Select for                                                                                     | Example (March 2026) |
| ---------------------- | ---------------------------------------------------------------------------------------------- | -------------------- |
| Primary planning agent | High instruction fidelity, cooperative tone, reads context before acting                       | Claude Opus 4.6      |
| Challenger             | Assertive reasoning, willingness to sustain objections under pushback, strong analytical depth | GPT-5.4              |

Why contrast matters: a cooperative model critiquing itself tends to produce weak pushback, while an assertive model doing nuanced planning tends to ignore boundaries. The challenger pattern works because the critic and the planner are optimized for different jobs.

These examples are guidance, not permanent prescriptions. Reassess when models update or when your observed failure modes change.

## Adoption guidance

1. Copy `challenger.agent.template.md` into `.github/agents/challenger.agent.md`.
2. Add `agents: ["challenger"]` to the planning agent that should use it.
3. Apply the prompt wiring from `prompt-wiring-examples.md` to the relevant prompt files.
4. If your planning model tends to overstate unsupported claims, also copy `examples/instructions/evidence-grounding.instructions.md` into `.github/instructions/`.

## Known limitation

Model-decided challenger invocation is still a soft guarantee. Under task pressure, the primary agent may skip the challenger. The visible output contract is what makes absence detectable to the user.
