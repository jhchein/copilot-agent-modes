# VS Code Copilot 2026 Update Suggestions

This note summarizes relevant recent customization features and recommends how this repo should stay current.

## Relevant current features

Based on current docs (approved 2026-02-04):

1. **Custom agents** are the current model (renamed from chat modes).
2. **Handoffs** are first-class in agent frontmatter for guided multi-step flows.
3. **Prompt files** remain useful as slash-command wrappers and task templates.
4. **Tool precedence** is explicit:
   1) prompt `tools`  
   2) referenced agent `tools`  
   3) selected agent defaults
5. **Agent Skills** are now supported and portable (VS Code, Copilot CLI, Copilot coding agent).
6. **Visibility/invocation controls** are available (`user-invokable`, `disable-model-invocation`) for both agents and skills.

## Recommended adjustments for this repository

### 1) Keep the current split (now adopted)

- `.github/agents/*.agent.md` for role behavior and handoffs.
- `.github/prompts/*.prompt.md` for ergonomic slash commands.
- `project-spec/` as project-specific source of truth.

### 2) Add a minimal skill baseline next

Create `.github/skills/` with 3-4 foundational skills from `agent-skills-design.md`:

- `constraint-check`
- `minimal-change-delivery`
- `root-cause-analysis`
- `fact-only-docs`

Mark noisy skills `user-invokable: true` and background helper skills `user-invokable: false`.

### 3) Add explicit tool policies in agent frontmatter

For safer defaults:

- thinker/exploration/architect: read-only/search-heavy tool sets
- execution/debugger: include edit + terminal tools
- documenter: read/search/edit (docs only)

This hardens behavior by role instead of relying only on prompt text.

### 4) Add one orchestration example agent (optional)

Introduce a single coordinator agent that can call selected subagents (`agents: [...]`) for “research → implement → verify” flows. Keep this optional to avoid complexity until needed.

### 5) Keep compatibility notes concise

Document that repository-level agents should avoid relying on properties that are ignored outside VS Code contexts, and keep behavior robust when optional fields are unavailable.

## Suggested rollout order

1. Add `.github/skills/` with 4 baseline skills.
2. Add tool allowlists per agent.
3. Add one optional coordinator agent only if multi-agent orchestration is repeatedly needed.

## Source references

- VS Code custom agents:  
  https://raw.githubusercontent.com/microsoft/vscode-docs/main/docs/copilot/customization/custom-agents.md
- VS Code prompt files:  
  https://raw.githubusercontent.com/microsoft/vscode-docs/main/docs/copilot/customization/prompt-files.md
- VS Code agent skills:  
  https://raw.githubusercontent.com/microsoft/vscode-docs/main/docs/copilot/customization/agent-skills.md
- GitHub custom agent configuration reference:  
  https://docs.github.com/en/copilot/reference/custom-agents-configuration
