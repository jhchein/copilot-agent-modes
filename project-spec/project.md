# Project

## Overview

- **Name**: AzureML parallel-pipeline-validation
- **One-liner**: Public reference repo showing how to run a custom Docker-based validation/test framework as an AzureML parallel job — processing thousands of multi-source blob sequences without Docker-in-Docker.

## Deliverables

1. **Public GitHub repo** — contains only the `pipeline/` sample code and a self-contained README (blog style with a reference to "I encountered this problem with a customer...; I am aware this is highly specific, but might help you ..."). No customer-specific context, engagement scaffolding, or project-spec material is published. Anyone with an AzureML workspace should be able to follow the README and submit the sample pipeline.
2. **Email to customer** — short, high-signal message that (a) explains the architectural insight (your Docker image IS the environment), (b) walks through the dispatch-table pattern, (c) links to the public repo, and (d) maps the generic sample to the customer's specific situation. See `project-spec/decisions/2025-02-12/delivery-format.md`.

## Goals

- Show how to use a custom Docker image (e.g. Julia + Python + test tooling) **as** the AzureML environment — avoiding Docker-in-Docker.
- Demonstrate AzureML SDK v2 parallel jobs to process thousands of ~1GB sequences from multiple blob stores concurrently.
- Provide a working sample MLTable dispatch-table structure for multi-source input data (sequences, labels, third data).
- Provide copy-and-adapt code, not a production-ready pipeline.
- Be publishable as a **public** repo — fully generic, no customer-identifying content.

## Non-goals

- Production pipeline code — this is a reference/sample only.
- Docker-in-Docker workaround documentation (unsupported, fragile).
- Infrastructure-as-Code for AzureML workspace, compute, or datastores.
- CI/CD pipeline setup.
- Data processing logic beyond a minimal placeholder.
- Generating the dispatch CSV — the sample includes a static CSV. In production, this is typically output of an upstream training or data preprocessing step.

## Tech Stack

- **Languages**: Python 3.10+, YAML
- **Frameworks**: AzureML SDK v2 (`azure-ai-ml`), Docker
- **Hosting/Cloud**: Azure Machine Learning (AmlCompute clusters, Blob Storage datastores)
- **Testing**: pytest
- **CI/CD**: GitHub Actions (runs pytest suite on push/PR — does not submit to AzureML)

## Target Audience

**Primary (repo)**: Any AzureML user who needs to run a custom Docker-based validation/test framework as a parallel job across blob-stored data from multiple sources.

**Secondary (email)**: The specific customer team integrating their existing Docker-based test framework into AzureML with parallel execution.

## System Shape

```
┌─────────────────────────────────────────────────────────────┐
│  AzureML Pipeline                                           │
│                                                             │
│  ┌─────────────────┐                                        │
│  │ Upstream step    │  (training, data prep, etc.)           │
│  │ (not in sample)  │                                        │
│  └────────┬────────┘                                        │
│           │ produces dispatch CSV                            │
│           ▼                                                  │
│  ┌────────────────┐    ┌──────────────────────────────────┐ │
│  │  MLTable        │──▶│  Parallel Job (N instances)       │ │
│  │  (dispatch      │   │  ┌────────────────────────────┐  │ │
│  │   table: CSV    │   │  │ Custom Docker Env           │  │ │
│  │   with 3 URI    │   │  │ (Julia + Python + tools)    │  │ │
│  │   columns)      │   │  │                             │  │ │
│  └────────────────┘   │  │ entry_script.py              │  │ │
│                        │  │  ├─ gets DataFrame row       │  │ │
│                        │  │  ├─ resolves 3 blob URIs    │  │ │
│                        │  │  ├─ downloads via fsspec    │  │ │
│                        │  │  ├─ runs validation (subprocess) │ │
│                        │  │  └─ returns result row      │  │ │
│                        │  └────────────────────────────┘  │ │
│                        └──────────────────────────────────┘ │
│                                   │                          │
│                                   ▼                          │
│                        ┌───────────────────┐                │
│                        │  Output datastore  │                │
│                        │  (appended CSV)    │                │
│                        └───────────────────┘                │
└─────────────────────────────────────────────────────────────┘
```

## Public Repo Layout

The public repo contains **only** the following. No `project-spec/`, `docs/`, or `.github/` engagement scaffolding.

```
README.md                           # Self-contained: problem, approach, structure, how-to-adapt
pipeline/
├── environment/
│   ├── Dockerfile                  # Custom AzureML environment (Julia + Python + tools)
│   ├── requirements.txt            # Pinned Python dependencies for the environment
│   └── validate.sh                 # Placeholder test framework (subprocess target)
├── data/
│   ├── sample_dispatch.csv         # Sample dispatch table (3 URI columns, long-form)
│   └── MLTable                     # MLTable YAML (read_delimited on the CSV)
├── src/
│   └── entry_script.py             # Parallel job entry script (init/run/shutdown)
├── tests/
│   ├── test_mltable.py             # MLTable loads and parses CSV correctly
│   ├── test_entry_script.py        # Entry script run() contract (mocked fsspec + subprocess)
│   └── test_pipeline_yaml.py       # pipeline.yml has required keys and valid structure
└── pipeline.yml                    # AzureML pipeline definition (parallel job, mode: direct)
.github/
└── workflows/
    └── test.yml                    # CI: runs pytest on push/PR
LICENSE
```

## Data

- **Input**: Thousands of sequences (~1GB each) across multiple Azure Blob Storage accounts/containers. Each validation requires data from three sources (sequence recording, labels, third data).
- **Dispatch table**: A CSV listing the three URIs per sequence. In production, this is typically **output of an upstream pipeline step** (training run, data preprocessing, or a registered dataset). The sample includes a static CSV for demonstration.
- **Output**: Validation/test results aggregated via `append_row_to` into a single CSV-like output file.
