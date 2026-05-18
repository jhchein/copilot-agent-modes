---
name: Docker Environment
description: Custom AzureML environment Dockerfile conventions for the validation framework
applyTo: "**/Dockerfile"
---

# Docker Environment Instructions

## Purpose

The Dockerfile defines the custom AzureML environment that **is** the test/validation container. We do NOT run Docker-in-Docker — this image runs directly as the AzureML job environment.

## Base Image

- You can use **any** Docker image as the AzureML environment — it does not have to be an AzureML base image. Images hosted on Docker Hub, Azure Container Registry, or any accessible registry work.
- AzureML provides maintained base images (e.g. `mcr.microsoft.com/azureml/openmpi4.1.0-ubuntu22.04`) that include common ML dependencies (OpenMPI, CUDA, Miniconda). Use them when their dependencies overlap with your needs. See [AzureML-Containers repo](https://github.com/Azure/AzureML-Containers) for the full list and Dockerfiles.
- For this project, a custom image makes sense since the test framework requires Julia + specific tooling that AzureML base images don't include.
- Install Julia explicitly. Pin the Julia version for reproducibility.

## Layer Ordering

1. OS-level packages (`apt-get`)
2. Language runtimes (Julia, Python)
3. Python packages (`pip install`)
4. Julia packages
5. Test framework / tooling
6. Entry point or working directory setup

## Security

- Never embed secrets, credentials, or real storage keys in the Dockerfile.
- Do not install Docker CLI or Docker Engine — DinD is not used.
- Prefer non-root user if the test framework allows it.
- Use `--no-cache-dir` for pip installs to reduce image size.

## AzureML Compatibility

- Three ways to provide the environment to AzureML (choose one):
  1. **Pre-built image**: Push to any accessible registry (ACR, Docker Hub, etc.) and reference via `image:` in the environment YAML.
  2. **Build context**: Provide the Dockerfile + supporting files and reference via `build.path:` — AzureML builds and caches the image in the workspace's ACR automatically.
  3. **Conda spec on base image**: Reference a base Docker image + conda YAML — AzureML layers conda on top. Not recommended here since Julia isn't installable via conda.
- For this project, option 2 (build context) is simplest for the sample — no manual image push needed. For production, option 1 with a CI-built image is more robust.
- `python` must be available on `PATH` — AzureML's parallel job infrastructure invokes the entry script as a Python process. This is implied by the architecture, not explicitly documented, but will fail without it.
- If the test framework is a standalone binary, the entry script should shell out to it via `subprocess`.
