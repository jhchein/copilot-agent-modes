---
name: Python Scripts
description: Entry script and AzureML SDK v2 submission code conventions
applyTo: "**/*.py"
---

# Python Script Instructions

## Entry Script (parallel job)

> **Decisions**: See `project-spec/decisions/2025-02-11/mltable-as-dispatch-table.md`, `sample-implementation-defaults.md`, and `project-spec/decisions/2025-02-12/review-feedback.md`.

- The entry script is invoked by AzureML's parallel job infrastructure for each mini-batch.
- Implement these functions:
  - `init()` — **required.** Called once per worker process. Minimal setup (logging, constants). No workspace env-var caching needed since the CSV stores long-form URIs.
  - `run(mini_batch)` — **required.** Called once per mini-batch. `mini_batch` is a **pandas DataFrame** (tabular mode). Each row has columns `sequence_path`, `label_path`, `third_data_path` containing **long-form** `azureml://` URI strings.
  - `shutdown()` — **optional.** Called once when the worker is done, for cleanup.

### Data access via `azureml-fsspec`

- Use `azureml-fsspec` as the primary data access method. Mention `azure-storage-blob` SDK as an alternative in a comment.
- The CSV stores **long-form URIs** (`azureml://subscriptions/{sub}/resourcegroups/{rg}/workspaces/{ws}/datastores/{ds}/paths/{path}`). The `AzureMachineLearningFileSystem` constructor accepts these directly — no URI expansion needed.
- Parse the long-form URI to extract the datastore base URI (everything up to `/paths/`) and the file path (after `/paths/`).
- Cache `AzureMachineLearningFileSystem` instances per datastore in a dict to avoid re-creating them per row.
- Managed identity on the compute cluster must have `Storage Blob Data Reader` on each storage account. `azureml-fsspec` handles credential passthrough automatically.

### Return value and output

- Return a **pandas DataFrame** from `run()` with one row per input row. Columns: `sequence_path` (str — for traceability, since output order is not guaranteed), `status` (str — `"pass"` or `"fail"`), `exit_code` (int), `message` (str).
- The **count of items returned** is used by AzureML to measure success — it must match the number of input rows processed.
- To aggregate results across all mini-batches, configure `task.append_row_to` in the parallel job YAML (e.g. `${{outputs.job_output_file}}`). Without this, return values are not collected into a single output.

### Other rules

- If using `argparse` in `init()` or `run()`, use `parse_known_args()` instead of `parse_args()` — AzureML injects additional arguments that will cause `parse_args()` to fail.
- Handle errors gracefully: log failures with enough context to identify which sequence failed, but do not crash the worker.

## Calling the Test Framework

- If the test framework is a CLI tool (e.g. a Julia script or binary), invoke it via `subprocess.run()`.
- Capture stdout/stderr and include them in the result or log.
- Set appropriate timeouts for `subprocess.run()` to prevent hung workers.

## Logging

- Use Python's `logging` module. AzureML captures stdout/stderr automatically.
- Never log secrets, storage keys, or SAS tokens.
- Log the input file path and a correlation ID for traceability.

## Dependencies

- Keep Python dependencies minimal — the heavy lifting is done by the test framework in the Docker image.
- Pin versions in `requirements.txt` or the Dockerfile.

## Style

- Use type hints for function signatures.
- Keep the entry script focused: data routing + framework invocation + result collection.
- Complex logic belongs in the Docker image / test framework, not in the entry script.
