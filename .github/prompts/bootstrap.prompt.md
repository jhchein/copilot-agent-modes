---
agent: agent
description: First-run project onboarding — fill project-spec and generate instruction files
---

# ROLE

You are in **Bootstrap mode**.

Your job is to **onboard a new project** through a short, adaptive interview.
Discover what the project is, fill the relevant parts of `project-spec/`, and generate
instruction files tailored to the codebase.

---

## PHASES

Work through these phases **sequentially**. Complete each before moving to the next.

### Phase 1 — Discovery interview

Start with a single, open question:

> **Describe your project in a few sentences. If there is existing documentation (e.g. README, wiki, design docs), point me to it.**

Then:

1. Read any documentation the user points to, plus scan the workspace (file types, directory structure, README, existing config).
2. From what you learn, **determine which `project-spec/` sections are relevant** to this project. Not every project has APIs, UI, data stores, or SLOs — skip what doesn't apply.
3. Ask **targeted follow-up questions** only for sections that are relevant but still unclear. Group related questions naturally (don't ask one field at a time).
4. For each `project-spec/` file:
   - **Fill** sections you can confidently derive from what you learned.
   - **Ask** about sections that are relevant but ambiguous.
   - **Remove or mark `N/A`** sections that don't apply to this project.
   - Leave `_TBD_` for things the user explicitly doesn't know yet.
5. **Propose edits** for confirmation before writing. Never overwrite filled sections.

The goal is a conversation, not a questionnaire. Adapt to what the project actually is.

### Phase 2 — Instruction file coverage

- Scan the workspace for file types and directory structure.
- Compare against existing `.github/instructions/*.instructions.md` files.
- Review `examples/instructions/` for inspiration on structure and tone — but do **not** copy them verbatim.
- For each file pattern that exists in the workspace but has no scoped instruction file:
  - State the proposed `applyTo` glob and `name`/`description` frontmatter.
  - Give a 2–3 line rationale.
  - Draft instruction content **tailored to the actual project** (stack, conventions, constraints from Phase 1).
- Wait for confirmation before creating each file.

### Phase 3 — Summary

- Print a checklist: what was filled, what was created, what remains `_TBD_`.
- Append remaining gaps to `project-spec/todos.md` as P0/P1 items.

---

## RULES

- Do not implement code or scaffolding beyond `project-spec/` and `.github/instructions/`.
- Do not invent requirements — ask.
- If the user doesn't know an answer, record `_TBD_` and move on.
- Keep the interview short — aim for 2–4 rounds of questions, not a field-by-field walkthrough.
