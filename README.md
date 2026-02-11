# copilot-agent-modes

> Opinionated, project-independent agent modes for GitHub Copilot — copy into your repo, run `/bootstrap`, start building.

## What is this?

A ready-to-use set of Copilot prompt files that give you **structured agent modes** (think → explore → architect → execute → debug → document), **always-on guardrails**, and a **project-spec scaffolding** that gets filled interactively.

## Quick start

1. Use this template (or copy `.github/` and `project-spec/` into your repo).
2. Open Copilot Chat and run `/bootstrap`.
3. Answer the questions — it fills `project-spec/` and generates instruction files tailored to your codebase.
4. Use the modes as you work.

## Workflow

```text
/bootstrap       → onboard project, fill spec, generate instruction files
     ↓
/thinker         → explore options, no commitment
/exploration     → divergent thinking, 3–6 options with trade-offs
     ↓
/architect       → collapse into one structure, define contracts
     ↓
/execution       → implement agreed changes
     ↓
/debugger        → diagnose issues, fix only with proof
/documenter      → record what exists, nothing more
```

## What's included

```text
.github/
  copilot-instructions.md          # Always-on guardrails (constitutional)
  prompts/                         # Slash-command modes
    bootstrap.prompt.md            # /bootstrap — first-run onboarding
    thinker.prompt.md              # /thinker  — planning, options as real options
    exploration.prompt.md          # /exploration — divergent thinking, 3–6 options
    architect.prompt.md            # /architect — structure, contracts, boundaries
    execution.prompt.md            # /execution — implement agreed changes
    debugger.prompt.md             # /debugger — diagnose, minimal fix only
    documenter.prompt.md           # /documenter — record facts, nothing more
  instructions/                    # Path-scoped rules (empty — created per project)
project-spec/                      # Project-specific source of truth (TBD placeholders)
examples/instructions/             # Reference instruction files for inspiration
```

## Design principles

- **Always-on instructions are a constitution**, not a playbook — short, behavioral, every token earns its place.
- **Prompt files are modes**, invoked via slash commands — each has a clear scope and boundary.
- **Instruction files are path-scoped rules**, auto-applied by file pattern — generated per project by `/bootstrap`.
- **Project-spec is the only project-specific content** — everything else is reusable across projects.

## Customizing

- **Modify any prompt** to match your working style — they're just Markdown files.
- **Add modes** by creating new `.prompt.md` files in `.github/prompts/`.
- **Instruction files** are generated per-project. See `examples/instructions/` for what good ones look like.
- **Project-spec** placeholders use `_TBD_` — fill them via `/bootstrap` or manually.

## License

[MIT](LICENSE)
