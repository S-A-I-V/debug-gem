---
tags: [cloud, infrastructure, platform]
aliases: [gcp, google-cloud, google-cloud-platform]
---
# GCP (Google Cloud Platform)

Google's cloud platform. Strong in data/AI (BigQuery, Vertex AI), Kubernetes (GKE), and serverless (Cloud Run).

## Known For These Error Patterns
- [[Permission & Authorization Errors]] — `PERMISSION_DENIED`, service account key management
- [[Resource Exhaustion Errors]] — `RESOURCE_EXHAUSTED`, quota limits
- [[Configuration & Environment Errors]] — Project scoping, ADC (Application Default Credentials)

## Key Concepts
- Everything is project-scoped — wrong project = resource not found
- Application Default Credentials (ADC) — `gcloud auth application-default login` for local dev
- Service accounts — use for production, don't commit keys

## Related
- [[AWS]], [[Azure]] — competing platforms
- [[Kubernetes]] — GKE is Google's managed K8s
- [[MOC — Cloud & Infrastructure]]

## My Notes

