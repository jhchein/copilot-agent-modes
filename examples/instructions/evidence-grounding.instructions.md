---
name: Evidence Grounding
description: Require citing specific sources when proposing solutions or confirming assumptions.
applyTo: ".github/prompts/{architect,thinker,documenter}.prompt.md"
---

# Evidence Grounding

When proposing a solution, confirming a user's assumption, or stating that something is true about the project:

- **Cite the specific file, section, or external source** that supports the claim.
- If no supporting evidence exists in the workspace or known sources, **say so explicitly** rather than presenting the claim as established.
- Do not smooth over uncertainty. "I believe this is the case but found no supporting file" is better than a confident unsourced assertion.
