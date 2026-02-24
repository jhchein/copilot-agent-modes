# ToDos

A prioritized list of open questions and next steps.

## P0 (blockers)

- None

## P1 (next)

- [ ] Rewrite README for external audience (see scope below).
- [ ] Add example `project-spec/` content to `examples/` (illustrative project archetypes, not this project's specs).

### README rewrite scope

**Audience:** Developer evaluating this template for first use.
**Voice:** Human, direct, conversational — not internal docs.
**Structure:**

1. **Why** (2-3 sentences): what problem this solves, who it's for.
2. **What you get** (brief): the six modes, guardrails, project-spec scaffolding.
3. **Quick start** (4 steps, as-is but with the bootstrap hint).
4. **Workflow** (keep the existing ASCII diagram — it works).
5. **Optional patterns** (NEW section, 3-5 lines): writing quality, evaluation harness, project-spec hygiene. Link to `examples/`.
6. **What's included** (keep the file tree).
7. **How it works** (collapse "Prompt files vs custom agents" + "Delegation model" + "Design principles" into one shorter section — an adopter needs the mental model, not the internal design rationale).
8. **Customizing** (keep as-is).

**Constraints:**

- Do not add aspirational language or marketing copy.
- Keep total length under the current README length (aim to trim ~20%).
- Preserve all structural information — restructure, don't remove.
- Move the detailed delegation model explanation (handoffs, subagents, why not subagents) to `docs/` if needed — the README should link to it, not contain it.

## P2 (later)

- [ ] Implement bootstrap Phase 0 (existing content detection) per `project-spec/decisions/2026-02-18/bootstrap-existing-content-detection.md`. Demoted from P1 — observed usage pattern (providing project context in the bootstrap message) sidesteps the detection problem. Low urgency unless adoption patterns show otherwise.

- Design Phase 2: tool allowlists per agent frontmatter (see `docs/design/vscode-copilot-2026-update-suggestions.md` §3).
- Monitor `root-cause-analysis` activation quality over 2-4 weeks per evaluation criteria in `docs/design/skills-phase1-contract.md`.
- Revisit guardrail skills (`constraint-check`, `minimal-change-delivery`, `fact-only-docs`) only if observed, repeatable failure modes justify them.
- Revisit whether agent prompts should defer to skills for shared concerns (only if context budget becomes measurably tight).
