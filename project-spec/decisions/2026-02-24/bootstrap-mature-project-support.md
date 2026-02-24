# Bootstrap mature project support

- **Date**: 2026-02-24
- **Status**: accepted
- **Context**: The bootstrap prompt assumes a mostly-greenfield adoption path. Phase 1's discovery interview treats all workspace code as equally authoritative. In mature projects, the workspace contains both modern conventions and legacy debt. The agent has no mechanism to distinguish them, so it averages patterns into mediocre specs. The primary failure mode reported by adopters of mature projects: "agents do not follow my conventions because there is no structural way to express which conventions are current and which are deprecated."
- **Decision**:
  1. **Do not add maturity detection heuristics to Phase 0.** The agent already reads docs and scans the workspace in Phase 1 step 1. Maturity is discoverable from what the agent reads — it does not need a separate detection mechanism.
  2. **Enrich Phase 1 with a single conventions probe** (step 3), triggered when the workspace contains substantial existing code. The probe covers both sides in one conversational beat:
     - **Deprecated patterns** (asked first — concrete, easy to answer): What libraries, approaches, or conventions should agents never use or replicate? Recorded in `constraints.md` under `## Deprecated patterns`.
     - **Reference code** (asked second — more abstract): Are there specific directories or files that represent the current desired conventions? If identified, `project-spec/` content is derived primarily from those paths. If not, noted as a P1 gap (not P0 — bootstrap can proceed without it).
  3. **Add `## Deprecated patterns` to the `constraints.md` TBD template** in the bootstrap prompt. This section is useful for both greenfield and mature projects — even new projects accumulate deprecated patterns over time.
  4. **Phase 2 references golden path context from Phase 1.** If the user identified reference directories, Phase 2 weights those paths when deriving conventions for instruction file content.
  5. **The conventions probe is conditional on existing code, not on detected conflicts.** Pattern conflict detection requires sophisticated code analysis that an LLM won't reliably perform during a conversational interview. "Substantial existing code" is observable from directory structure.
  6. **The ROLE description acknowledges both new and existing projects.**
- **Alternatives considered**:
  - _Explicit maturity detection in Phase 0_ (git history depth, dependency count, file count): Rejected — over-engineered. The agent already has workspace scanning in Phase 1. Adding terminal commands for heuristic detection adds friction for marginal accuracy gain.
  - _Separate "mature project" bootstrap prompt_: Rejected — creates two prompts to maintain. The conditional probes integrate naturally into the existing interview flow.
  - _User self-selects "new vs mature" at start_: Rejected — unnecessary friction. The agent can discover this from what it reads.
- **Consequences**: `bootstrap.prompt.md` Phase 1 gains one merged conventions probe (~8 lines). Phase 2 gains one line referencing golden path context. `constraints.md` TBD template gains one section (~3 lines). ROLE description updated (1 word). No new files, phases, or layers. Greenfield behavior is unchanged — the conventions probe is skipped for workspaces without substantial existing code.
- **Reversibility**: Two-way door. Removing the probes restores original Phase 1 behavior. The `## Deprecated patterns` template section is inert if left as `_TBD_`.
