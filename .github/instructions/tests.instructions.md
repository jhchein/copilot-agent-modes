---
name: Tests
description: Pytest conventions for pipeline sample tests
applyTo: "**/tests/**/*.py"
---

# Test Instructions

> **Decision**: See `project-spec/decisions/2025-02-12/review-feedback.md` §5.

## Framework

- Use **pytest**. No additional test frameworks or plugins required.
- Tests live in `pipeline/tests/`.
- No `conftest.py` needed unless shared fixtures grow beyond trivial.

## Test Files

### `test_mltable.py`

- Load the MLTable from `pipeline/data/` using the `mltable` Python package.
- Assert it parses into a pandas DataFrame.
- Assert column names: `sequence_path`, `label_path`, `third_data_path`.
- Assert row count matches `sample_dispatch.csv`.
- Assert all cell values are non-empty strings.

### `test_entry_script.py`

- Test `run()` with a mock mini-batch DataFrame (constructed in-test, not loaded from CSV).
- Mock `AzureMachineLearningFileSystem` — do not hit real blob storage.
- Mock `subprocess.run` — return a `CompletedProcess` with configurable returncode/stdout/stderr.
- Assert `run()` returns a DataFrame with columns: `sequence_path`, `status`, `exit_code`, `message`.
- Assert row count of output matches input.
- Test both the success path (`returncode=0` → `status="pass"`) and failure path (`returncode=1` → `status="fail"`).
- Test exception handling (e.g. download failure → `status="fail"`, `exit_code=-1`).

### `test_pipeline_yaml.py`

- Load `pipeline/pipeline.yml` with `pyyaml`.
- Assert top-level keys: `$schema`, `type: pipeline`, `jobs`.
- Assert `jobs.validate.type` is `parallel`.
- Assert `jobs.validate.inputs.dispatch_table.mode` is `direct`.
- Assert `jobs.validate.task.append_row_to` is present.
- Assert `jobs.validate.task.entry_script` is `entry_script.py`.
- Assert `jobs.validate.mini_batch_size` is present and is a string.

## Mocking Strategy

- Use `unittest.mock.patch` for `AzureMachineLearningFileSystem` and `subprocess.run`.
- Do not mock pandas — use real DataFrames for inputs and assert on real DataFrames for outputs.
- Mocked `AzureMachineLearningFileSystem.download` should create an empty file at the destination path (since `validate.sh` / subprocess is also mocked).

## CI

- GitHub Actions workflow at `.github/workflows/test.yml`.
- Trigger: push and pull_request on `main`.
- Matrix: Python 3.10+ (single version is fine for a reference repo).
- Install: `pip install pytest pyyaml mltable azureml-fsspec pandas`.
- Run: `pytest pipeline/tests/ -v`.
- Tests must NOT require an AzureML workspace, Azure credentials, or network access.

## Style

- Follow the same conventions as the entry script: type hints, clear names.
- Keep tests focused — one assertion concept per test function.
- Use `pytest.fixture` for shared setup (e.g. constructing a mock mini-batch DataFrame).
