# Optional quality and scenario patterns for the public template

- **Date**: 2026-02-22
- **Status**: accepted
- **Context**: The forked implementation validated two useful extensions: a writing-quality toolchain for outward-facing documentation and a scenario-based review pattern for stakeholder perspectives. Both add value, but this template is intentionally minimal and project-agnostic. The template should preserve low default complexity while still making these patterns easy to adopt.
- **Decision**:
  1. Keep the template baseline minimal by default. Do not pre-enable writing or scenario workflows in every generated repository.
  2. Provide reusable optional patterns for these workflows.
  3. Use `/bootstrap` to discover context and propose opt-in generation of optional patterns on explicit user confirmation.
  4. Preserve the skills-layering policy: default skills are capability-oriented; guardrail-heavy skills remain opt-in unless repeated failures justify promotion.
  5. Keep generated artifacts path-scoped and reversible.
- **Alternatives considered**:
  - _Enable writing and scenario workflows by default_: Rejected — higher baseline complexity and unnecessary surface area for many projects.
  - _Do nothing_: Rejected — loses validated, reusable patterns from the fork.
  - _Add only docs-level instructions without bootstrap support_: Rejected — discoverability and adoption become inconsistent.
- **Consequences**: The root template remains lightweight while gaining a first-class path for optional quality tooling. `/bootstrap` guidance becomes more important because optional patterns are activated through explicit user choice.
- **Reversibility**: Two-way door. Optional pattern files can be removed without affecting core mode behavior.
