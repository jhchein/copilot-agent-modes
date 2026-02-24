# Example Instruction Files

These are **reference examples** showing how to write path-scoped `.instructions.md` files.

They are **not active** — Copilot only loads instruction files from `.github/instructions/`.

## How to use

1. Copy an example to `.github/instructions/`.
2. Adjust the `applyTo` glob to match your project's file structure.
3. Edit the rules to reflect your project's conventions.

Or run `/bootstrap` in Copilot Chat — it will scan your workspace and propose instruction files automatically.

## Examples

| File                           | Scope                                | Purpose                                          |
| ------------------------------ | ------------------------------------ | ------------------------------------------------ |
| `api.instructions.md`          | `src/api/**/*.py`                    | API contracts, auth, observability               |
| `ingestion.instructions.md`    | `src/ingestion/**/*.py`              | Data pipelines, storage layout                   |
| `project-spec.instructions.md` | `project-spec/**/*.md`               | Project-spec consistency and placeholder hygiene |
| `terraform.instructions.md`    | `infra/**/*.tf`, `infra/**/*.tfvars` | IaC provider config, naming, networking          |
| `writing.instructions.md`      | `docs/**/*.md`                       | Outward-facing documentation quality             |

## Optional patterns

- **Writing Quality Pattern**:
  - `examples/instructions/writing.instructions.md`
  - `examples/skills/writing-quality/SKILL.md`
  - Optional prompt wiring in `documenter.prompt.md` (and optionally `architect.prompt.md`, `execution.prompt.md`)
- **Evaluation Harness Pattern**:
  - `examples/scenarios/persona.template.md`
  - `examples/scenarios/review.prompt.template.md`
  - `examples/scenarios/evaluator.agent.template.md`
- **Project-Spec Hygiene Pattern**:
  - `examples/instructions/project-spec.instructions.md`
