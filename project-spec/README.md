# Project Spec

This folder is the **only project-specific source of truth** in this repository.
All prompts and instruction templates reference `project-spec/` — nothing project-specific lives elsewhere.

## How to use

Run `/bootstrap` in GitHub Copilot Chat. It will walk through each file, ask questions, and fill placeholders on confirmation.

Or fill manually:

1. Start with the essentials:
   - `project.md` — goals, stack, non-goals
   - `constraints.md` — security, privacy, networking
   - `todos.md` — open questions and next steps
2. Add detail as the project stabilizes:
   - `interfaces.md` — API/auth contracts
   - `infrastructure.md` — cloud/IaC conventions
   - `decisions/` — architectural decisions (one file per decision)

## Principles

- Keep it concise: write what is necessary to build and operate the system.
- Prefer "must/must not" for constraints.
- Avoid secrets/PII. Reference secret names/locations instead.
- If a section is unknown, write `_TBD_` and add a question to `todos.md`.
