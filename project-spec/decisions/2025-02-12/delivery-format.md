# Delivery Format: Public Repo + Email

- **Date**: 2025-02-12
- **Status**: accepted
- **Context**: We need to deliver the AzureML parallel validation pattern to the customer. The code is already generic by design (placeholder URIs, no customer data). We considered private repo, zip file, long-form document, and public repo options.

## Decision

Deliver as **two artifacts**:

### 1. Public GitHub repo

Contains only the `pipeline/` sample code, a self-contained `README.md`, and an MIT `LICENSE`. No engagement context, no customer-identifying information.

**What's in the public repo:**

```
README.md
LICENSE
pipeline/
├── environment/
│   ├── Dockerfile
│   └── validate.sh
├── data/
│   ├── sample_dispatch.csv
│   └── MLTable
├── src/
│   └── entry_script.py
└── pipeline.yml
```

**What is NOT in the public repo:**

- `project-spec/` (engagement scaffolding)
- `docs/` (problem statement with customer name/email)
- `.github/` (copilot instructions, prompts — these are authoring aids, not deliverables)
- `examples/` (internal reference material)

**README requirements:**

The README must be self-contained and answer these questions for a cold reader:

1. What problem does this solve? (DinD doesn't work in AzureML; here's the alternative)
2. What's the architecture? (dispatch table → parallel job → custom environment → subprocess)
3. What's in the repo? (file-by-file explanation)
4. How do I adapt this? (what to replace, where to plug in your framework)
5. Prerequisites? (AzureML workspace, compute, datastores, managed identity)

### 2. Email to customer

Short, targeted message that:

- Reminds of the call context and the DinD dead-end
- Delivers the key insight: "your Docker image IS the AzureML environment"
- Explains the dispatch-table pattern at a high level
- Links to the public repo
- Maps the generic sample to the customer's specific situation (three blob stores, Julia framework, sequence recordings)
- Notes that the dispatch CSV is typically output of their upstream training/prep step
- Offers a follow-up call if needed

**Email outline:**

```
Subject: AzureML parallel validation — sample repo

1. Context reminder (1-2 sentences: the DinD question + our call)
2. Key insight (2-3 sentences: image = environment, no DinD needed)
3. The pattern (3-4 sentences: dispatch table CSV → MLTable → parallel job
   → entry script downloads data → shells out to your framework)
4. Dispatch table origin (1-2 sentences: in production, output of your
   training run or data prep step — the sample has a static CSV)
5. Link to repo + what to look at first (README, then pipeline.yml)
6. What to adapt (replace Dockerfile contents, validate.sh, CSV columns)
7. Open items (managed identity setup, VM sizing, output format)
8. Offer for follow-up
```

The email is NOT stored in the repo. It's a one-time communication artifact.

## Rationale

| Factor                       | Public repo                     | Private repo         | Zip/email attachment     |
| ---------------------------- | ------------------------------- | -------------------- | ------------------------ |
| Sharing friction             | Low (URL)                       | High (GitHub access) | Medium (file management) |
| Updateability                | You can push fixes              | Same                 | None (snapshot)          |
| Reusability                  | Other engagements, community    | No                   | No                       |
| Customer identification risk | None (code is generic)          | Low                  | Low                      |
| Maintenance burden           | Minimal (snapshot + disclaimer) | Same                 | None                     |

The code is already fully generic — publishing it costs nothing and gains easy sharing + reuse.

## Consequences

- The private working repo (this one) remains the authoring environment with project-spec, instructions, and engagement context.
- The public repo is a **separate GitHub repository** — not a branch or subdirectory of this repo.
- Publishing requires a final review pass: grep for customer names, real URIs, or engagement-specific language.
- README quality is critical — it must stand alone without the email.
- Add a disclaimer: "This is a reference sample, not production code. Snapshot, not actively maintained."

## Alternatives considered

| Option                                      | Why not                                                                           |
| ------------------------------------------- | --------------------------------------------------------------------------------- |
| Long-form document (PDF/Word)               | Harder to update, can't be cloned/forked, code samples go stale                   |
| Private repo shared via collaborator invite | GitHub account friction; customer may be in Azure DevOps ecosystem                |
| Single email with inline code               | Code without surrounding structure is hard to run; no version history             |
| Publish this entire repo                    | Contains engagement context, customer name, problem statement with personal email |
