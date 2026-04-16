---
tags: [cloud, infrastructure, platform]
aliases: [azure, microsoft-azure]
---
# Azure (Microsoft Azure)

Microsoft's cloud platform. Strong in enterprise (Active Directory integration), .NET ecosystem, and hybrid cloud.

## Known For These Error Patterns
- [[Permission & Authorization Errors]] — `AuthorizationFailed`, RBAC role missing
- [[Configuration & Environment Errors]] — Subscription/resource group scoping, managed identity config
- [[Resource Exhaustion Errors]] — Subscription quota limits

## Key Concepts
- Resources scoped to Subscription → Resource Group → Resource
- Azure AD for identity, Azure RBAC for resource access — different systems
- Managed Identity — system-assigned vs user-assigned

## Debug Tools
- Azure Monitor, Application Insights, Log Analytics (KQL queries)

## Related
- [[AWS]], [[GCP]] — competing platforms
- [[C#]] — native language for Azure SDKs
- [[MOC — Cloud & Infrastructure]]

## My Notes

