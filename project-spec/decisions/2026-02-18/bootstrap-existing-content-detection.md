# Bootstrap existing content detection

- **Date**: 2026-02-18
- **Status**: accepted
- **Context**: The template repo ships with `project-spec/` filled for its own project (dogfooding). When an adopter copies the template and runs `/bootstrap`, the existing "never overwrite filled sections" rule causes bootstrap to skip the interview — it sees filled content and assumes it belongs to the adopter's project. Adopters must manually clear project-spec before bootstrapping, which contradicts the "copy, bootstrap, go" pitch.
- **Decision**:
  1. **Add Phase 0 to bootstrap** — an existing-content check that runs before the discovery interview.
  2. **Phase 0 scans fill-in files** (`project.md`, `constraints.md`, `interfaces.md`, `infrastructure.md`, `todos.md`) for non-TBD content.
  3. **If existing content is found**, bootstrap summarizes what it sees and asks: "Is this your project, or are you starting fresh from the template?"
  4. **"Start fresh"** resets fill-in files to TBD templates and asks whether to also clear `decisions/` and `design/` contents.
  5. **"This is mine"** proceeds with the existing "never overwrite filled sections" rule intact.
  6. **Phase 0 is silent when not needed** — if all fill-in files are TBD/empty, bootstrap skips straight to Phase 1 (identical to current behavior).
  7. **TBD template content is inline in the bootstrap prompt**, not referenced from external files. The templates are small and stable; external file dependency adds complexity for no gain.
  8. **`project-spec/README.md` is never touched** — it's structural, not project-specific.
- **Alternatives considered**:
  - _Gitignore / skip-worktree project-spec_: Rejected — prevents team collaboration on project specs in adopter repos. Solves the maintainer's problem at the cost of the adopter's workflow.
  - _Two-repo factory/template split_: Deferred — right architecture if adoption scales, but premature given unknown demand. Adds ongoing sync maintenance. Can be revisited if distribution becomes a real concern.
  - _Move filled project-spec to examples/_: Rejected — the filled content is this project's real spec, not sample content. Moving it conflates two purposes.
  - _Reset project-spec to TBD before each push_: Too manual, easy to forget, and loses dogfooding value in the working repo.
- **Consequences**: `bootstrap.prompt.md` gains a Phase 0 section (~15-20 lines). No changes to other agents, prompts, or skills. Bootstrap prompt grows slightly but remains under reasonable context budget. Re-runs on already-bootstrapped projects work naturally — user says "this is mine" and the interview proceeds as before.
- **Reversibility**: Two-way door. Phase 0 is a few lines in one prompt file. Removing it restores original behavior with no side effects.
