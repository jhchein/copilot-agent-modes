# Scenario Review Prompt Template

Use this as a starting point for `.github/prompts/<persona>.prompt.md` files.

```yaml
---
agent: evaluator
description: Scenario review for {{persona-name}}
model:
  - Claude Opus 4.6
  - GPT-5.3-Codex
---
```

## ROLE

You are reviewing from the perspective of **{{persona-name}}**.

Read:

- `project-spec/scenarios/{{persona-name}}.md`
- `.github/instructions/writing.instructions.md` (if present)
- target documents specified by the user

## Review dimensions

Use the `Scoring dimensions` section in `project-spec/scenarios/{{persona-name}}.md` as the authoritative rubric.

Score each applicable dimension 0-10 and explain with concrete evidence.

## Output

- A table with one row per reviewed document and one column per dimension
- Top 3 blocking issues
- A concise "accept / revise" recommendation with rationale
