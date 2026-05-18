---
name: AzureML Pipeline & Data
description: SDK v2 pipeline YAML, parallel job config, and MLTable conventions
applyTo: "**/*.yml, **/*.yaml"
---

# AzureML Pipeline & Data Instructions

## SDK Version

- Target **AzureML SDK v2** (`azure-ai-ml`). Do not use SDK v1 (`azureml-sdk`) patterns.
- Pipeline definitions use YAML format (`$schema: https://azuremlschemas.azureedge.net/latest/pipelineJob.schema.json`).

## Parallel Job Configuration

> **Decision**: The MLTable is a tabular dispatch table (`mode: direct`). See `project-spec/decisions/2025-02-11/mltable-as-dispatch-table.md`.

- Use `type: parallel` for the validation step.
- Set `input_data` to reference the MLTable via `${{inputs.<name>}}`.
- Use `mode: direct` — required for tabular mltable input. The parallel job splits the table by estimated physical size.
- Set `mini_batch_size: "1kb"` — this is physical size, not row count. With ~200-300 byte CSV rows, `"1kb"` targets ~1-3 rows per mini-batch (one sequence = one validation unit). Include a YAML comment explaining this.
- `max_concurrency_per_instance` controls parallel workers per node. Default: 1 (GPU) or number of cores (CPU). Set to `1` unless the entry script is thread-safe.
- Configure `mini_batch_error_threshold` (YAML attribute) for fault tolerance on failed mini-batches. For item-level error thresholds, use the program argument `--error_threshold`.
- Set `retry_settings.max_retries` and `retry_settings.timeout` for transient failures.
- Compute resources: set `resources.instance_count` for the number of nodes.
- The `mltable` pip package must be listed in the environment's conda/pip dependencies.

## MLTable (Dispatch Table)

- The MLTable is a **tabular dispatch table**: a CSV where each row is one validation unit.
- Columns: `sequence_path`, `label_path`, `third_data_path` — each containing an `azureml://datastores/<name>/paths/<path>` URI.
- The MLTable YAML uses `read_delimited` to parse the CSV.
- The parallel job receives rows as pandas DataFrames; the entry script extracts URIs and handles data access.
- URIs in the CSV are **strings** — AzureML does NOT auto-mount them. The entry script must resolve data access (e.g. via `azureml-fsspec`, Azure Storage SDK, or secondary mounted inputs).
- Sample data must use **placeholder paths** — never real customer data.

## Environment Definition

- Use **build context** (`build.path:` in the task environment) for the sample — AzureML builds and caches the image in the workspace's ACR automatically.
- For production, switch to a pre-built image with CI-built tags (`image:` pointing to the ACR-hosted image).
- The build context path should point to `./environment/` (relative to `pipeline/`), which contains the Dockerfile and `validate.sh`.

## Placeholders

- Use `<your-subscription-id>`, `<your-workspace-name>`, `<your-resource-group>`, `<your-compute-cluster>` for customer-specific values.
- Add comments explaining what each placeholder should be replaced with.
