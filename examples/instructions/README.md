# Example Instruction Files

These are **reference examples** showing how to write path-scoped `.instructions.md` files.

They are **not active** — Copilot only loads instruction files from `.github/instructions/`.

## How to use

1. Copy an example to `.github/instructions/`.
2. Adjust the `applyTo` glob to match your project's file structure.
3. Edit the rules to reflect your project's conventions.

Or run `/bootstrap` in Copilot Chat — it will scan your workspace and propose instruction files automatically.

## Examples

| File                        | Scope                                | Purpose                                 |
| --------------------------- | ------------------------------------ | --------------------------------------- |
| `api.instructions.md`       | `src/api/**/*.py`                    | API contracts, auth, observability      |
| `ingestion.instructions.md` | `src/ingestion/**/*.py`              | Data pipelines, storage layout          |
| `terraform.instructions.md` | `infra/**/*.tf`, `infra/**/*.tfvars` | IaC provider config, naming, networking |
