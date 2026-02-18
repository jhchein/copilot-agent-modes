# ToDos

A prioritized list of open questions and next steps.

## P0 (blockers)

- None

## P1 (next)

- [ ] Implement bootstrap Phase 0 (existing content detection) per `project-spec/decisions/2026-02-18/bootstrap-existing-content-detection.md`.
- [ ] Add example `project-spec/` content to `examples/` (illustrative project archetypes, not this project's specs).

## P2 (later)

- Design Phase 2: tool allowlists per agent frontmatter (see `project-spec/design/vscode-copilot-2026-update-suggestions.md` §3).
- Monitor `root-cause-analysis` activation quality over 2-4 weeks per evaluation criteria in `skills-phase1-contract.md`.
- Revisit guardrail skills (`constraint-check`, `minimal-change-delivery`, `fact-only-docs`) only if observed, repeatable failure modes justify them.
- Revisit whether agent prompts should defer to skills for shared concerns (only if context budget becomes measurably tight).
