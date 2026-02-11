# Bootstrap GHCP Agent Prompts

Project-independent prompt templates and scaffolding for GitHub Copilot agent mode.

Copy this structure into any repo to get a working set of agent modes, guardrails, and project-spec templates — then run `/bootstrap` to fill in the project-specific details.

## What's included

```text
.github/
  copilot-instructions.md          # Always-on guardrails (constitutional)
  prompts/                         # Slash-command modes
    bootstrap.prompt.md            # /bootstrap — onboard a new project
    thinker.prompt.md              # /thinker  — explore options, no commitment
    exploration.prompt.md          # /exploration — divergent thinking, 3–6 options
    architect.prompt.md            # /architect — shape structure, contracts, boundaries
    execution.prompt.md            # /execution — implement agreed changes
    debugger.prompt.md             # /debugger — diagnose, fix only with proof
    documenter.prompt.md           # /documenter — record what exists, nothing more
  instructions/                    # Path-scoped rules (empty — created per project)
project-spec/                      # Project-specific source of truth (TBD placeholders)
examples/instructions/             # Reference instruction files to copy and adapt
```

## Quick start

1. Copy `.github/` and `project-spec/` into your repo.
2. Open Copilot Chat and run `/bootstrap`.
3. Answer the questions — it fills `project-spec/` and proposes instruction files.
4. Use `/thinker`, `/architect`, `/execution`, etc. as you work.

## Design principles

- **Always-on instructions are a constitution**, not a playbook — short, behavioral, no fluff.
- **Prompt files are modes**, invoked via slash commands — each has a clear scope and boundary.
- **Instruction files are path-scoped rules**, auto-applied by file pattern — created per project.
- **Project-spec is the only project-specific content** — everything else is reusable.
