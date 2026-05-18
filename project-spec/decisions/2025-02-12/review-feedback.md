# Review Feedback — Simplification and Quality Pass

- **Date**: 2025-02-12
- **Status**: accepted
- **Context**: After initial P0 implementation, a review identified several improvements to simplify the sample code, improve quality signals, and make the repo more useful as a public reference. All changes are two-way doors.

## Decisions

### 1. Long-form URIs in dispatch CSV (amends sample-implementation-defaults §1)

**Change**: The dispatch CSV now stores **long-form URIs** (`azureml://subscriptions/{sub}/resourcegroups/{rg}/workspaces/{ws}/datastores/{ds}/paths/{path}`) instead of short-form.

**Rationale**: Eliminates ~40 lines of URI expansion code (`_expand_short_uri`, `_SHORT_URI_RE`, env-var caching in `init()`). The entry script becomes dramatically simpler — `init()` is minimal, `_download_file` takes a URI and constructs the `AzureMachineLearningFileSystem` directly. Nobody reads production CSVs by hand; longer URIs are an acceptable trade-off for code simplicity.

**Consequences**:

- `sample_dispatch.csv` — URIs become ~150 chars each (wider but still readable in any CSV tool).
- `entry_script.py` — remove `_expand_short_uri`, `_SHORT_URI_RE`, env-var globals. `init()` becomes trivial. `_download_file` parses the long-form URI directly.
- `README.md` — update URI references and the `entry_script.py` description.
- If short-form URI support is needed later, add a separate `pipeline/src/uri_helper.py` utility. Mention in README.

### 2. Remove DinD commentary from Dockerfile

**Change**: Strip the "no Docker-in-Docker" explanation from the Dockerfile header comment. Keep only the functional purpose ("Custom AzureML environment for the validation framework").

**Rationale**: The audience is technical. The README covers the DinD context for anyone who needs it. The Dockerfile should be a clean, professional Dockerfile — not a tutorial.

### 3. Add `requirements.txt` with pinned dependencies

**Change**: Create `pipeline/environment/requirements.txt` with pinned versions of `mltable`, `azureml-fsspec`, and `pandas`. The Dockerfile references it via `COPY requirements.txt . && pip install --no-cache-dir -r requirements.txt`.

**Rationale**: Pinned deps are a best practice for reproducibility. The `.github/instructions/python.instructions.md` already calls for this but the initial implementation used unpinned `pip install` in the Dockerfile.

### 4. Rewrite README introduction

**Change**: Lead with what the repo does (the positive pattern), not with the DinD failure. The DinD anecdote moves to a brief aside or the "Key Concepts" section. The opening paragraph should describe the problem being solved and the approach.

**Rationale**: Leading with "Docker-in-Docker doesn't work" may discourage readers or make them think the repo is about DinD workarounds. The core value is the dispatch-table + parallel-job + custom-environment pattern.

### 5. Add tests (`pipeline/tests/`)

**Change**: Add a `pipeline/tests/` directory with pytest-based tests. Tests ship in the public repo.

**Test matrix**:

| Test file               | What it validates                                                                                                                 | Dependencies                                                                  |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| `test_mltable.py`       | MLTable YAML loads and parses `sample_dispatch.csv` into correct DataFrame shape and columns                                      | `mltable`, `pandas`                                                           |
| `test_entry_script.py`  | `run()` produces correct output DataFrame shape given mock inputs; error handling works                                           | `pytest`, `pandas`, mock `AzureMachineLearningFileSystem` + mock `subprocess` |
| `test_pipeline_yaml.py` | `pipeline.yml` is valid YAML, contains required keys (`type: parallel`, `mode: direct`, `mini_batch_size`, `append_row_to`, etc.) | `pyyaml`                                                                      |

**Framework**: `pytest` — boring, universal, no config needed.

**Why public**: Tests in the repo signal quality. They also serve as executable documentation of expected behavior — a reader can see exactly what the entry script's input/output contract is by reading the tests.

### 6. Parameterize `pipeline.yml` values

**Change**: Replace hardcoded `instance_count: 2`, `max_concurrency_per_instance: 1`, `mini_batch_size: "1kb"`, and compute target with clearly marked placeholders and comments explaining what each value does and how to tune it.

**Note**: AzureML SDK v2 pipeline YAML does not support variable substitution for parallel job settings (`instance_count`, `mini_batch_size`, etc.) — they are static YAML values. The parameterization is via comments and placeholder markers, not runtime variables. A Python submission script (`submit_pipeline.py`) that sets these programmatically remains a P2 option.

### 7. GitHub Actions CI (nice-to-have)

**Change**: Add `.github/workflows/test.yml` to the public repo that runs the pytest suite on push/PR.

**Scope**: Runs only the pytest tests (MLTable parsing, entry script unit tests, YAML validation). Does NOT submit pipelines to AzureML — that requires a workspace and credentials.

**Rationale**: CI on a public reference repo signals that the code is maintained and working. It also catches regressions if we update any files. Minimal maintenance burden since the tests are self-contained.

### 8. No MLTable creation script

**Decision**: Do not create a script to generate dispatch CSVs or MLTable YAML.

**Rationale**: The MLTable YAML is 5 lines and doesn't change when the CSV changes (it just reads the CSV with `read_delimited`). A generation script would over-engineer a trivial artifact. The README documents the format; users drop their CSV next to the MLTable file.

## Execution order

```
1. Switch CSV to long-form URIs
2. Simplify entry_script.py (remove URI expansion)
3. Clean up Dockerfile comments
4. Add requirements.txt + update Dockerfile
5. Rewrite README intro
6. Add pipeline/tests/ (3 test files)
7. Parameterize pipeline.yml
8. Add .github/workflows/test.yml
```

Items 1-4 are tightly coupled (entry script depends on URI format). Items 5-8 are independent of each other.
