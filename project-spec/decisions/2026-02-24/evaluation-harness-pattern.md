# Evaluation harness pattern and evaluator agent

- **Date**: 2026-02-24
- **Status**: accepted
- **Context**: The optional scenario-review pattern validated in downstream projects as a practical quality loop: artifacts are reviewed against stakeholder scenarios with explicit scores and blocking findings. The template lacked a reusable evaluator agent example, and used inconsistent naming (`szenario`).
- **Decision**:
  1. Add a reusable `evaluator` agent as the generic critic role for scenario-based reviews.
  2. Keep persona-specific concerns in `project-spec/scenarios/*.md` and prompt files, not in the evaluator agent.
  3. Rename "optional packs" to "optional patterns" in bootstrap and docs.
  4. Keep writing quality single-sourced in `.github/instructions/writing.instructions.md`; skills and review prompts reference this source when present.
  5. Add active meta-evaluation scenarios for this repository (`new-adopter`, `template-contributor`) as a holdout-style quality loop used regularly but not as a hard CI gate.
- **Alternatives considered**:
  - _Keep only scenario templates without an evaluator agent example_: Rejected — weak discoverability and inconsistent implementation quality.
  - _Make evaluator project-specific instead of generic_: Rejected — reduces portability and duplicates logic across projects.
  - _Duplicate writing-quality rules across skills/prompts_: Rejected — risks drift and contradictory guidance.
- **Consequences**: The template gains one additional mode (`evaluator`) and a clearer optional evaluation pattern. Ongoing maintenance increases slightly due to scenario/prompt upkeep, but changes remain reversible and optional.
- **Reversibility**: Two-way door. Removing evaluator and scenario artifacts does not affect core build/execute/debug/document flows.
