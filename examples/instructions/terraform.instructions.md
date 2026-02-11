---
name: Terraform IaC
description: Terraform provider config, module patterns, naming, and networking rules
applyTo: "infra/**/*.tf, infra/**/*.tfvars"
---

# Terraform Instructions

## Provider Configuration

- Use two provider aliases: `azurerm` (default for workload) and `azurerm.network` (for central networking/DNS).
- All private endpoints and DNS registration **MUST** use the `azurerm.network` provider.

## Module Pattern

```hcl
module "example" {
  source = "./modules/example"
  providers = {
    azurerm         = azurerm
    azurerm.network = azurerm.network
  }
}
```

## Naming Conventions

- Define a prefix and naming convention in `project-spec/infrastructure.md`.
- Keep names deterministic and environment-scoped.

## Networking

- Prefer disabling public network access on PaaS resources unless `project-spec/constraints.md` permits it.
- Private endpoint subnet, address space, and DNS strategy must be defined in `project-spec/infrastructure.md`.
