# Prompt Wiring for the Challenger Pattern

The challenger agent file must be supported by prompt-level enforcement. Without these prompt changes, the primary agent will often forget to invoke the challenger.

## 1. Add sub-agent access in the planning agent

In `thinker.agent.md` or `architect.agent.md`, add:

```yaml
agents: ["challenger"]
```

Optional user-triggered fallback handoff:

```yaml
- label: Stress-test this
  agent: thinker
  prompt: Before proceeding, invoke the challenger sub-agent to stress-test the current reasoning. Present its findings and your evaluation of each objection.
```

Use `architect` instead of `thinker` when wiring this fallback into the architect agent.

## 2. Add hard rules in the planning prompt

For architect-style prompts:

```markdown
## RULES

Do NOT:

- collapse options into a single recommended approach without first invoking the **challenger** sub-agent
- propose irreversible structural choices without first invoking the **challenger** sub-agent
```

For thinker-style prompts:

```markdown
## Forbidden

- narrowing to a single recommended option without first invoking the **challenger** sub-agent
- recommending irreversible commitments without first invoking the **challenger** sub-agent
```

## 3. Add a workflow sequence before recommendations

```markdown
When your output contains a recommendation or structural commitment:

1. Explore the design space and identify options
2. **Before forming your recommendation**, invoke the **challenger** sub-agent with your current reasoning
3. Evaluate each objection against evidence
4. Then form and present your recommendation
5. Include a **## Challenger Findings** section showing what the challenger raised and your evaluation of each point
```

Key point: challenge before drafting the recommendation, not after. Challenging a finished draft tends to produce defensive patching instead of genuine reconsideration.

## 4. Add a visible output contract

When the primary agent makes a recommendation, include:

```markdown
## Challenger Findings

- **Objection:** ...
  **Evaluation:** ...
- **Objection:** ...
  **Evaluation:** ...
```

If this section is absent, the user can immediately see that the challenger was skipped.

## 5. Optional interpretation guidance

If the primary model is highly cooperative, add guidance like:

```markdown
Evaluate challenger objections against evidence before adjusting your position. Do not capitulate simply because the challenger is confident.
```

Model-specific self-awareness clauses are optional. Use them only when you have observed a real, repeatable failure mode in your workspace.
