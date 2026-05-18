# Guardrail pattern examples

- **Date**: 2026-03-09
- **Status**: accepted
- **Context**: Observed failures in downstream adoption showed that some projects benefit from optional guardrail patterns in addition to the base agent modes. The recurring symptoms were: planning agents narrowing to recommendations without adequately stress-testing assumptions, and cooperative models presenting unsupported claims with too much confidence. The question is whether and how to upstream these lessons without bloating the default template.
- **Decision**:
  1. Add `examples/guardrails/` with a challenger agent template and prompt-wiring examples.
  2. Add `examples/instructions/evidence-grounding.instructions.md` as an optional instruction example.
  3. Do not modify default prompts or default agent files.
  4. Treat challenger as an optional second sub-agent exception pattern. Adopters who use it should record their own decision if they add it to live agent files.
  5. Defer retry-budget and intent-verification patterns until they are validated beyond a single adoption.
- **Alternatives considered**:
  - _Add a circuit breaker to the default execution prompt_: Rejected — too prescriptive for a model-agnostic template. Retry thresholds and escalation sinks are project-specific.
  - _Ship a broad guardrail patterns doc in `docs/`_: Rejected — the template can show a concrete optional pattern without introducing a broader canonical taxonomy.
  - _Ship the challenger agent without prompt-wiring examples_: Rejected — the agent file alone is insufficient. The prompt wiring is what makes the pattern reliable.
  - _Ship verifier alongside challenger_: Rejected — this would introduce a second new sub-agent exception class before the first optional one has proven reusable.
- **Consequences**: `examples/` gains one new optional pattern directory and one new instruction example. README and delegation docs should point to the examples. Default behavior remains unchanged.
- **Reversibility**: Two-way door. Removing the examples has no effect on current adopters.
