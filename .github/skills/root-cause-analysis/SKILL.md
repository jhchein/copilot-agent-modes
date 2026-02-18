---
name: root-cause-analysis
description: Structured debugging procedure: collect facts, rank hypotheses by confidence, test with disconfirming evidence, propose fixes only at high confidence. Use when diagnosing errors, failures, or unexpected behavior.
argument-hint: "[error message or symptom description]"
---

# Root Cause Analysis

Follow this structured diagnostic procedure when investigating failures:

## 1. Collect facts

Gather all available evidence and categorize it:

- **Observed:** Error messages, logs, stack traces, reproduction steps directly seen in output
- **Reported:** Information stated by the user but not independently verified
- **Assumed:** Inferences from context (mark explicitly as assumptions)

Present findings in a **Facts** section.

## 2. Identify unknowns

List what information is missing or unclear in an **Unknowns** section:

- What would narrow the search space?
- What critical context is unavailable?

If critical unknowns exist, ask clarifying questions before proceeding to hypotheses.

## 3. Generate hypotheses

Produce 2-5 ranked, **falsifiable** hypotheses in a table:

| # | Hypothesis | Supporting evidence | Disconfirming test | Confidence |
|---|------------|--------------------|--------------------|------------|

Each hypothesis must be testable. If it can't be falsified, it's speculation, not a hypothesis.

## 4. Test hypotheses

Start with the highest-confidence hypothesis. Seek **disconfirming** evidence first (not confirming evidence).

Update the hypothesis table with test results and revised confidence levels.

## 5. Propose fix (only when ALL conditions are met)

- Root cause identified with high confidence
- Fix scope is local (no architectural changes)
- Fix is reversible
- Fix is directly tied to a stated, tested hypothesis

## 6. If no hypothesis reaches high confidence

Report findings with:

- Updated hypothesis table
- Specific next diagnostic steps recommended
- No speculative fixes
