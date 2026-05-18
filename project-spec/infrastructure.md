# Infrastructure

How the system is deployed and operated.

## IaC

- Tooling: N/A — this repo does not provision infrastructure. The customer's existing AzureML workspace is assumed.

## Prerequisites (customer-provided)

The sample code assumes the following resources already exist:

| Resource                        | Purpose                                                               |
| ------------------------------- | --------------------------------------------------------------------- |
| AzureML Workspace               | Hosts pipelines, environments, compute                                |
| AmlCompute Cluster              | Runs parallel job instances (CPU or GPU depending on test framework)  |
| Azure Blob Storage datastore(s) | Stores sequence recordings, labels, and third data                    |
| Azure Container Registry (ACR)  | Hosts the custom Docker environment image (attached to the workspace) |

## Compute

- Parallel jobs target an **AmlCompute cluster** with auto-scaling. The customer should size `max_instances` based on desired parallelism and budget.
- VM size depends on the test framework's requirements (memory, GPU). _TBD_ by customer.

## Data Access

- Blob stores are registered as **AzureML datastores** (credential-based or identity-based access).
- Input mode: `ro_mount` (read-only mount) for large sequence files.
- Output mode: `rw_mount` or `upload` for validation results.
