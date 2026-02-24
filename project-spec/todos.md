# ToDos

A prioritized list of open questions and next steps.

## P0 (blockers)

- None

## P1 (next)

- [x] Rewrite README for external audience — done; detailed delegation model moved to `docs/delegation-model.md`.
- [x] Bootstrap mature project support — golden path probe, deprecated patterns probe, constraints template update. Decision: `project-spec/decisions/2026-02-24/bootstrap-mature-project-support.md`.
- [ ] Add example `project-spec/` content to `examples/` (illustrative project archetypes, not this project's specs).

## P2 (later)

- [x] Implement bootstrap Phase 0 (existing content detection) per `project-spec/decisions/2026-02-18/bootstrap-existing-content-detection.md`. Implemented in `bootstrap.prompt.md` Phase 0 (lines 27–56).

- Design Phase 2: tool allowlists per agent frontmatter (see `docs/design/vscode-copilot-2026-update-suggestions.md` §3).
- Monitor `root-cause-analysis` activation quality over 2-4 weeks per evaluation criteria in `docs/design/skills-phase1-contract.md`.
- Revisit guardrail skills (`constraint-check`, `minimal-change-delivery`, `fact-only-docs`) only if observed, repeatable failure modes justify them.
- Revisit whether agent prompts should defer to skills for shared concerns (only if context budget becomes measurably tight).
