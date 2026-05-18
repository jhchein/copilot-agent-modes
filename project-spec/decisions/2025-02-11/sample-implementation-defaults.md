# Sample Implementation Defaults

- **Date**: 2025-02-11
- **Status**: accepted
- **Context**: With the MLTable dispatch table decision made, we need to resolve several implementation-level choices before writing the four P0 sample files. All choices here are two-way doors — cheap to change after writing.

## Decisions

### 1. Data access approach: `azureml-fsspec`

Use `azureml-fsspec` as the primary data access method in the sample entry script. Mention `azure-storage-blob` SDK as an alternative in a comment.

**Rationale**: `azureml-fsspec` is AzureML-native, handles credential passthrough automatically (uses the compute's managed identity or workspace credential), and keeps the sample focused on AzureML patterns.

**URI format**: ~~The CSV stores short-form URIs~~ **Amended 2025-02-12**: The CSV now stores **long-form URIs** (`azureml://subscriptions/{sub}/resourcegroups/{rg}/workspaces/{ws}/datastores/{ds}/paths/{path}`) directly. This eliminates the need for URI expansion code in the entry script. See `project-spec/decisions/2025-02-12/review-feedback.md` §1.

### 2. Dockerfile fidelity: placeholder `validate.sh`

Include a trivial shell script (`pipeline/environment/validate.sh`) in the Docker image that the entry script calls via `subprocess.run()`. This shows the complete flow (image → entry script → subprocess → test framework) without us pretending to know the customer's actual framework.

The script:

- Accepts file paths as arguments
- Prints a placeholder validation message
- Exits 0 (success)

This is `COPY`'d into the image at build time, demonstrating the separation: **test framework lives in the Docker image, entry script is uploaded by AzureML at job time.**

### 3. `mini_batch_size`: `"1kb"`

Set `mini_batch_size: "1kb"` in the pipeline YAML. Each CSV row with three `azureml://` URIs is ~200-300 bytes, so `"1kb"` targets ~1-3 rows per mini-batch. This aligns with the customer's use case (one sequence = one validation unit).

Include a YAML comment explaining the physical-size semantics.

### 4. `run()` output shape

Each `run()` call returns a **single-row pandas DataFrame** per input row with columns:

| Column          | Type | Description                                                       |
| --------------- | ---- | ----------------------------------------------------------------- |
| `sequence_path` | str  | The input URI (for traceability — output order is not guaranteed) |
| `status`        | str  | `"pass"` or `"fail"`                                              |
| `exit_code`     | int  | Subprocess exit code                                              |
| `message`       | str  | Summary or error detail                                           |

Combined with `task.append_row_to`, this produces a CSV-like output file aggregating all results.

### 5. Environment provisioning: build context

Use build context (`build.path:` in the pipeline YAML) so the sample works without a manual `docker push` step. AzureML builds and caches the image in the workspace's ACR automatically.

For production, the customer should switch to a pre-built image with CI-built tags for reproducibility.

### 6. Execution order

The four P0 files have this dependency structure:

```
CSV + MLTable  ──┐
                 ├──▶  pipeline.yml
Dockerfile     ──┤
                 │
entry_script.py ─┘
```

Suggested build order:

1. `sample_dispatch.csv` + `MLTable` (no dependencies, trivial)
2. `Dockerfile` + `validate.sh` (no dependencies on other files)
3. `entry_script.py` (references CSV column names and validate.sh path)
4. `pipeline.yml` (references all three above)

## Alternatives considered

| Choice                    | Alternative              | Why not                                                                                                            |
| ------------------------- | ------------------------ | ------------------------------------------------------------------------------------------------------------------ |
| `azureml-fsspec`          | `azure-storage-blob` SDK | More boilerplate (construct BlobServiceClient, parse account URLs), less AzureML-native                            |
| Long-form URIs in CSV     | Short-form URIs in CSV   | Short-form saves ~80 chars/cell but requires ~40 lines of URI expansion code; simplicity wins (amended 2025-02-12) |
| `validate.sh` placeholder | Real Julia script        | Would scope-creep; we don't know the customer's framework                                                          |
| Build context             | Pre-built image          | Requires manual `docker build` + `docker push` before the sample works                                             |
| `"1kb"` mini-batch        | `"100b"` or `"2kb"`      | `"1kb"` is a reasonable middle ground; easily tunable                                                              |
