# Scenario: Template Contributor

## Role

- Maintainer extending this template while preserving layering discipline and reversibility.

## Decision concerns

- Does each change respect the five-layer model and avoid hidden coupling?
- Are additions evidence-driven, or are they speculative complexity?
- Can changes be rolled back without breaking baseline workflows?

## Risk tolerance

- Low tolerance for architectural drift or unclear layer ownership.
- Low tolerance for default-on features that increase baseline complexity.

## Review focus

- Alignment with accepted decisions in `project-spec/decisions/`.
- Explicit one-way vs two-way door treatment for structural changes.
- Maintenance cost transparency for optional patterns.

## Scoring dimensions

Score each dimension 0-10 with concrete evidence.

1. **Layering integrity**: Changes respect layer responsibilities and avoid hidden coupling.
2. **Decision coherence**: Changes align with accepted decision records.
3. **Reversibility**: Structural changes remain cheap to undo.
4. **Maintenance cost clarity**: Ongoing cost is explicit for optional patterns.
5. **Writing quality**: Clarity and precision align with `.github/instructions/writing.instructions.md` when available.
