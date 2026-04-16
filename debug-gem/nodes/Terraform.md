---
tags: [infrastructure, iac, devops]
aliases: [terraform, tf, hcl]
---
# Terraform

Infrastructure as Code tool by HashiCorp. Declarative configuration in HCL. Manages cloud resources across providers.

## Known For These Error Patterns
- [[Concurrency & Race Condition Errors]] — State lock conflicts, resource modified outside Terraform
- [[Dependency & Import Errors]] — Circular dependencies, provider version conflicts
- [[Configuration & Environment Errors]] — Backend config, variable not set, wrong provider config
- [[Syntax & Parse Errors]] — HCL syntax errors
- [[State & Lifecycle Errors]] — State drift, resource already exists

## Key Concepts
- State file contains all resource info including secrets — encrypt and secure it
- `plan` before `apply` — always review changes
- State drift = manual changes outside Terraform — detect with `plan`
- `count` vs `for_each` — `for_each` is more stable (keyed, not indexed)

## Related
- [[AWS]], [[GCP]], [[Azure]] — cloud providers
- [[Kubernetes]] — can manage K8s resources
- [[MOC — Cloud & Infrastructure]]
- [[MOC — DevOps & CI-CD]]

## My Notes

