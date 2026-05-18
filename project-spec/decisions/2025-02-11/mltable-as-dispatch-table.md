# MLTable as Dispatch Table (Tabular Mode)

- **Date**: 2025-02-11
- **Status**: accepted
- **Context**: The validation pipeline must process thousands of sequences, each requiring data from three sources (sequence recording, labels, third data) stored across multiple Azure Blob Storage accounts. We needed to decide how to structure the parallel job input.

## Decision

Use a **tabular MLTable** as a dispatch table. The MLTable reads a CSV where each row is one validation unit with columns pointing to the three data sources:

```
sequence_path,label_path,third_data_path
azureml://datastores/recordings/paths/seq_001.bin,azureml://datastores/labels/paths/seq_001.csv,azureml://datastores/thirddata/paths/seq_001.dat
azureml://datastores/recordings/paths/seq_002.bin,azureml://datastores/labels/paths/seq_002.csv,azureml://datastores/thirddata/paths/seq_002.dat
```

The parallel job uses `mode: direct` and splits this table so each worker receives a DataFrame mini-batch (one row with `mini_batch_size` set appropriately). The entry script extracts the three URIs from the row and resolves data access itself.

## Consequences

- **Input mode**: `mode: direct` (required for tabular mltable).
- **`mini_batch_size`**: Estimated physical size of rows (e.g. `"2kb"`), not a file count.
- **Entry script signature**: `run(mini_batch)` receives a **pandas DataFrame**, not a list of file paths.
- **Data access**: The entry script is responsible for accessing the actual blob data using the URI strings from the DataFrame. Options: use AzureML datastore mounts as secondary inputs, download via `azureml-fsspec`, or use Azure Storage SDK with managed identity.
- **MLTable YAML**: Uses `read_delimited` transformation to parse the CSV.
- **`mltable` pip package** must be installed in the environment (required for tabular mltable inputs).

## Alternatives considered

| Option                                           | Description                                                          | Why rejected                                                                                                               |
| ------------------------------------------------ | -------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| **A — Single MLTable, tabular, with auto-mount** | Same structure, but expecting AzureML to mount the URIs in the cells | AzureML does not auto-mount URIs that appear as cell values in a tabular input — they're just strings                      |
| **B — URI folder, file-list mode**               | Point at a folder, split by file count                               | Data lives in multiple blob stores — no single folder to point at. Would require restructuring the customer's data layout. |
| **Separate MLTables per source**                 | One MLTable per data source, joined in the script                    | Complicates the pipeline YAML (three inputs to coordinate), harder to guarantee row alignment                              |

## Open questions

- How does the entry script authenticate to the blob stores to download data? Managed identity (compute cluster identity with Storage Blob Data Reader on each storage account) is the expected approach, but needs validation with the customer's setup.
- Should the sample demonstrate `azureml-fsspec` for blob access, or `azure-storage-blob` SDK? `azureml-fsspec` is simpler but less well-known.
