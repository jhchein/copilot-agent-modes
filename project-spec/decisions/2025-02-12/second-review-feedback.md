# Second Review Feedback — Quality and DX Pass

- **Date**: 2025-02-12
- **Status**: accepted
- **Context**: Second review of P1 implementation raised questions about tempfile usage, subprocess rationale, `AzureMachineLearningFileSystem` correctness, package versions, local dev workflow (uv), test completeness, and pipeline YAML parameterization.

## Decisions

### 1. `tempfile.TemporaryDirectory()` is correct — add comment

**Finding**: AzureML parallel jobs do NOT provide a per-mini-batch or per-worker scratch directory. `AZUREML_BI_OUTPUT_PATH` exists but is for batch endpoint deployments, not parallel pipeline jobs. No `AZ_BATCHAI_JOB_TEMP` or `AZUREML_CR_*` equivalent.

**Decision**: Keep `tempfile.TemporaryDirectory()`. Add a comment explaining the choice.

### 2. Add subprocess rationale comment

**Change**: Add a comment before the `subprocess.run()` call explaining why we shell out instead of calling a Python function: the validation framework is a CLI tool (Julia binary, shell script, etc.) that lives in the Docker image. The entry script is a data router, not a validation implementation.

### 3. `AzureMachineLearningFileSystem` is correctly parameterized

**Finding**: Constructor accepts a single `uri` argument — a long-form datastore URI. The `.download(rpath, lpath)` method takes `rpath` relative to the datastore root, which is exactly what `_parse_long_uri` produces. Auth is automatic via managed identity — no parameters needed.

**Decision**: No code change. Add a comment on `_get_fs()` noting that auth uses the compute's managed identity.

### 4. `requirements.txt` versions — defer to local validation

**Finding** (as of 2026-02):

| Package          | Current | Latest | Note                      |
| ---------------- | ------- | ------ | ------------------------- |
| `mltable`        | 1.6.1   | 1.6.3  | Python 3.12 support       |
| `azureml-fsspec` | 1.3.1   | 1.3.1  | Already latest            |
| `pandas`         | 2.2.2   | 3.0.0  | 3.0 requires Python ≥3.11 |

**Decision**: Keep current pins for now. Bumping versions must be validated against the local AzureML environment first. Stays a P2 item.

### 5. Add `pyproject.toml` + uv for local dev

**Decision**: Add a minimal `pyproject.toml` at `pipeline/` level with dev dependencies (`pytest`, `pyyaml`). The Dockerfile continues using `requirements.txt` (runtime only). Add a "Local Development" section to README with `uv sync` and `uv run pytest` commands. GitHub Actions stays pip-based (no uv dependency in CI).

Rationale: uv is fast and practical for local dev. It doesn't affect the AzureML image or CI. Two-way door.

### 6. Expand test coverage

**Missing tests identified**:

- `_parse_long_uri` unit tests — the URI parsing logic has edge cases (missing `/paths/`, empty path).
- Timeout test — `subprocess.TimeoutExpired` should produce a fail row with `exit_code=-1`.

**Decision**: Add `TestParseLongUri` class and a timeout test to `test_entry_script.py`.

### 7. Pipeline YAML parameterization — confirmed inline-only

**Finding**: AzureML SDK v2 pipeline YAML does NOT support `${{settings.xxx}}` variable substitution for parallel job settings. The `settings:` block only supports `default_datastore`, `default_compute`, `continue_on_step_failure`, `force_rerun`. No `max_concurrent_runs` key exists at pipeline level.

**Decision**: Keep comments-only parameterization (already done in P1). Add a `--set` override example to the README "How to Run" section as a runtime override option.

## Implementation notes

Issues discovered during local test execution (`uv run pytest tests/ -v`):

| Issue                                                               | Root cause                                                                                                                | Fix                                                                                                 |
| ------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| `uv sync` fails: no wheel for `azureml-dataprep-native`             | Python 3.13 selected; `azureml-dataprep-native` publishes binary-only wheels, no 3.13 build                               | Constrain `requires-python = ">=3.10,<3.13"`, run `uv sync --python 3.12`                           |
| `No module named 'pkg_resources'`                                   | `mltable` → `azureml-dataprep` imports `pkg_resources`; `setuptools` 82.0 removed it                                      | Pin `setuptools<81` in `pyproject.toml`                                                             |
| `No module named 'pipeline'` / `No module named 'src'`              | Tests used `from pipeline.src.entry_script import ...`; cwd is `pipeline/`, no `pipeline` package                         | Add `pythonpath = ["src"]` to `[tool.pytest.ini_options]`, import as `from entry_script import ...` |
| `Not able to find MLTable file from the MLTable folder`             | `load("pipeline/data")` resolved to `pipeline/pipeline/data` (doubled path)                                               | Change to `load("data")`                                                                            |
| `FileNotFoundError: pipeline/pipeline.yml`                          | Same doubling issue                                                                                                       | Change to `Path("pipeline.yml")`                                                                    |
| Exception test failure: `[WinError 2]` instead of `ConnectionError` | `_fs_cache` retained mocked FS from prior test → download "succeeded" with stale mock → subprocess failed on missing file | Add `autouse` fixture clearing `_fs_cache` before each test                                         |

Final result: 23 tests pass, 0 failures.
