# Constraints

## Security

- Secrets handling: Sample code must use **placeholders** (`<your-subscription-id>`, `<your-workspace-name>`, etc.). Never embed real connection strings, keys, or tenant IDs.
- Docker socket mount (`/var/run/docker.sock`) is **not used** — the clean approach avoids DinD entirely.

## Privacy & Data Handling

- PII policy: No real customer data in this repo. Sample MLTable entries use **fictional blob paths**.
- The customer's actual sequence data (~1GB/file) stays in their blob stores — this repo only references example URIs.

## Public Repo

- The published repo must contain **zero customer-identifying information**: no company names, no personal names, no project-specific references, no engagement context.
- All language must be generic ("your test framework", "your blob stores") — the repo serves any AzureML user with this pattern.
- `project-spec/`, `docs/`, and `.github/` (with engagement-specific prompts/instructions) are **excluded** from the public repo. They remain in the private working repo only.
- The email is the customer-specific artifact. It maps the generic repo to the customer's situation. The email lives outside this repo (sent directly).

## Licensing

- Public repo: MIT license (standard for reference/sample repos).
- The private working repo (this one) is not published.

## AzureML-Specific

- Custom Docker environments must be buildable from the Dockerfile alone (no private base images without documenting access). Ideally use an official recommended AzureML base image.
- Parallel job `mini_batch_size` should target **one sequence per mini-batch**. With tabular mode this is a physical size string (e.g. `"1kb"` for ~1 CSV row), not a row count. See `project-spec/decisions/2025-02-11/sample-implementation-defaults.md`.
- Prefer `ro_mount` over `download` for large blob inputs to avoid unnecessary data copies.
- The exact details of the docker environment (Julia version, test framework, dependencies) are actually not relevant to the sample code in this repo, since the entry script just shells out to the test framework. It would help to demonstrate how to install Julia and the test framework in the Dockerfile, but the entry script can be very generic (e.g. `subprocess.run(["/path/to/test_framework", mini_batch[0]])`), since the focus is on the AzureML integration patterns.
