---
name: writing-quality
description: Structured writing-quality pass for outward-facing docs to reduce AI signals and improve clarity.
argument-hint: "[docs file path or folder under docs/]"
---

# Writing Quality

Use this skill when creating or revising outward-facing documentation in `docs/`.

## Scope

- Allowed scope: files under `docs/`
- Out of scope: `project-spec/`, `.github/`, code files

If the requested file is outside `docs/`, stop and state that this skill only applies to outward-facing docs.

## Process

### 1. Read and orient

- Read the target document fully before editing.
- Identify audience and purpose from the document itself.
- Record initial risks (tone drift, repetitive structure, weak specificity).

### 2. Structural review

- Check heading hierarchy and section flow.
- Ensure table/list sections include interpretive prose where needed.
- Verify that claims are concrete and auditable.
- Check that Facts, Recommendations, and TBD items are explicitly labeled.

### 3. Vocabulary and pattern lint

Apply mechanical checks from `.github/instructions/writing.instructions.md`:

- Filler phrases and hedging language
- Repeated sentence patterns in adjacent lines
- Intensifier repetition
- Vague wording where concrete technical or business statements are needed
- Sentence-length variance (avoid monotonous rhythm)
- Repeated paragraph openings
- Mandatory first-person plural ("I", "we", "our") when expressing (team) judgments, trade-offs, and guidance. Never use passive/detached voice for architectural assessments (e.g., use "We consider this architecture optimal" instead of "This architecture is optimal").

For communication drafts, also check against `.github/instructions/communication.instructions.md`:

- Recipient/audience stated
- Action items with owners
- No confidential details in customer-facing drafts

For meeting notes, also check against `.github/instructions/meeting-notes.instructions.md`:

- Date, attendees, and agenda present
- Action items captured with owners and due dates
- Fact/Recommendation/TBD labels applied

### 4. Line-level polish

- Rewrite flagged passages with minimal semantic drift.
- Keep factual meaning unchanged.
- Prefer plain, specific language.
- Produce a concise before/after change log for non-trivial edits.

## Output Contract

For each edited document, provide:

1. A short summary of writing-quality issues found.
2. A concise list of major rewrites performed.
3. Any remaining limitations that were intentionally left unchanged.
