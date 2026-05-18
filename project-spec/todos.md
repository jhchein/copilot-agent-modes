# ToDos

A prioritized list of open questions and next steps.

## P0 — initial implementation (done)

1. [x] ~~Create `pipeline/data/sample_dispatch.csv` + `pipeline/data/MLTable`~~
2. [x] ~~Create `pipeline/environment/Dockerfile` + `pipeline/environment/validate.sh`~~
3. [x] ~~Create `pipeline/src/entry_script.py`~~
4. [x] ~~Create `pipeline/pipeline.yml`~~
5. [x] ~~Write top-level `README.md`~~

> Specs: `project-spec/decisions/2025-02-11/sample-implementation-defaults.md`

## P1 — review feedback (done)

> Specs: `project-spec/decisions/2025-02-12/review-feedback.md`

1. [x] ~~Switch `sample_dispatch.csv` to long-form URIs~~
2. [x] ~~Simplify `entry_script.py` — remove URI expansion code, simplify `init()`~~
3. [x] ~~Clean up Dockerfile header comments (remove DinD commentary)~~
4. [x] ~~Add `pipeline/environment/requirements.txt` with pinned deps; update Dockerfile to use it~~
5. [x] ~~Rewrite README intro (lead with pattern, DinD as aside)~~
6. [x] ~~Parameterize `pipeline.yml` (placeholders + comments for instance_count, concurrency, mini_batch_size, compute)~~
7. [x] ~~Add `pipeline/tests/test_mltable.py` — MLTable loads and parses CSV~~
8. [x] ~~Add `pipeline/tests/test_entry_script.py` — run() contract with mocked deps~~
9. [x] ~~Add `pipeline/tests/test_pipeline_yaml.py` — YAML structure validation~~
10. [x] ~~Add `.github/workflows/test.yml` — CI runs pytest on push/PR~~

## P1.1 — second review feedback (done)

> Specs: `project-spec/decisions/2025-02-12/second-review-feedback.md`

1. [x] ~~entry_script.py — add tempfile, subprocess, and auth comments~~
2. [x] ~~test_entry_script.py — add `_parse_long_uri` tests + timeout test~~
3. [x] ~~Create `pipeline/pyproject.toml` for uv local dev~~
4. [x] ~~README — add "Local Development" section + `--set` override example~~

## P1.2 — local test run fixes (done)

Fixes discovered by running `uv run pytest tests/ -v` locally:

1. [x] ~~`pyproject.toml`: constrain `requires-python = ">=3.10,<3.13"` — `azureml-dataprep-native` has no wheel for Python 3.13~~
2. [x] ~~`pyproject.toml`: add `setuptools<81` — `mltable` imports `pkg_resources`, removed in setuptools 81+~~
3. [x] ~~`pyproject.toml`: add `pythonpath = ["src"]` to `[tool.pytest.ini_options]` — allows `import entry_script` from tests~~
4. [x] ~~`test_mltable.py` / `test_pipeline_yaml.py`: fix relative paths (`"pipeline/data"` → `"data"`, `"pipeline/pipeline.yml"` → `"pipeline.yml"`) — cwd is already `pipeline/`~~
5. [x] ~~`test_entry_script.py`: fix import path to `entry_script` (not `pipeline.src.entry_script`)~~
6. [x] ~~`test_entry_script.py`: add `autouse` fixture to clear `_fs_cache` between tests — stale mocks leaked across test classes~~

All 23 tests pass. No warnings from project code (9 deprecation warnings from `azureml-dataprep` internals — upstream issue).

## P2 (before sharing)

- [x] ~~Validate `requirements.txt` versions against local AzureML environment~~ — current pins (`mltable==1.6.1`, `azureml-fsspec==1.3.1`, `pandas==2.2.2`) work locally. Bumping to `mltable==1.6.3` deferred (optional, not blocking).
- [ ] Draft email to customer (see `project-spec/decisions/2025-02-12/delivery-format.md`)
- [x] ~~Review all files for customer-identifying content~~ (scanned — clean)
- [ ] Add MIT `LICENSE` file to public repo root
- [x] ~~Decide on MLTable structure~~ → `project-spec/decisions/2025-02-11/mltable-as-dispatch-table.md`
- [x] ~~Design sample implementation defaults~~ → `project-spec/decisions/2025-02-11/sample-implementation-defaults.md`
- [x] ~~Decide on delivery format~~ → `project-spec/decisions/2025-02-12/delivery-format.md`
- [x] ~~Review feedback decisions~~ → `project-spec/decisions/2025-02-12/review-feedback.md`

## P3 (later / customer-dependent)

- [ ] Determine customer's test framework base image (Julia version, dependencies)
- [ ] Determine customer's output format for validation results
- [ ] Determine customer's VM size requirements (CPU vs GPU, memory)
- [ ] Validate managed identity access pattern for multi-store blob access
- [ ] Add a walkthrough doc: step-by-step guide to register environment, create compute, submit pipeline
- [ ] Consider adding a Python submission script (`submit_pipeline.py`) as alternative to CLI
- [ ] Document error handling and retry strategy for parallel jobs
- [ ] Document monitoring/logging approach for parallel validation runs
