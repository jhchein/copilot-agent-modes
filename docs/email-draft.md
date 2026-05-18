# Email Draft - Tobias Trostel

> **Do not publish in the public repo.** This document is for internal coordination only.

---

**Subject:** AzureML Parallel Validation - Sample Repo

---

Hi Tobias,

following up on our conversation, I put together a sample repo that walks through the approach end to end.

The short version: Docker-in-Docker most likely doesn't work on AzureML - the Docker socket isn't exposed on the compute nodes. But as we discussed, we don't actually need it. We build a Docker image as our AzureML environment - everything inside the image (Julia, Python, your test framework) is directly available to the job, no inner container required.

## The Pattern

The idea: we parallelize the validation and need to provide the data accordingly. A CSV file serves as a "dispatch table", listing the blob URIs per row / sequence for each validation unit (e.g. recording, labels, third data). This CSV is wrapped in an MLTable so the parallel job can automatically split it across workers. Each worker downloads its sequence / the three blobs via `azureml-fsspec` and calls the test framework e.g. via `subprocess`. Results are then aggregated via `append_row_to`.

In the pipeline, the dispatch table would typically be the output of an upstream pipeline step (e.g. directly from training or a data prep step). In this sample it's a static CSV, since I'm not sure how you handle this in your setup, and to keep everything standalone.

## Repo

**https://github.com/jhchein/azureml-parallel-validation**

Where to start:

1. **README** - overview of the pattern and architecture
2. **mltable** - just have a quick look at the dispatch table (CSV + MLTable YAML)
3. **pipeline.yml** - the pipeline definition
4. **src/entry_script.py** - the entry script

## What you'd need to adapt

- **Dockerfile**: your actual framework dependencies
- **validate.sh**: your actual framework invocation instead of the placeholder
- **sample_dispatch.csv**: adjust columns and URIs to match your blob layout. The three datastores in the sample (`recordings`, `labels`, `thirddata`) correspond to your three blob stores
- **Placeholders** e.g. in the URIs (`<your-subscription-id>` etc.) - replace with your actual values

## Open items on your side

- **Managed Identity**: the workspace managed identity needs `Storage Blob Data Reader` on all three storage accounts so that `azureml-fsspec` can access them without credentials
- **VM Sizing**: with ~1 GB per sequence, you could start testing with Standard_D-Series or Standard_F-Series if your framework is more CPU-bound, or Standard_E-Series if it's memory-bound - or potentially even GPU (NC/ND-series). D is probably the safest starting point if you're unsure, and you can always switch to a different SKU later.
- **Output Format**: the sample collects results as CSV via `append_row_to`. If you need more structured outputs (e.g. JSON, metrics in MLflow), we can of course adjust that

Happy to walk through this together - let me know if this works for you as is, or if we should look at it in a call.

Best regards,
Hendrik
