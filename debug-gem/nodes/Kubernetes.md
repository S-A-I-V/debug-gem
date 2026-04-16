---
tags: [infrastructure, containers, orchestration, devops]
aliases: [kubernetes, k8s, kube]
---
# Kubernetes

Container orchestration platform. Manages deployment, scaling, and networking of containerized applications.

## Known For These Error Patterns
- [[State & Lifecycle Errors]] — `CrashLoopBackOff`, `Pending`, `Terminating` stuck states
- [[Resource Exhaustion Errors]] — `OOMKilled`, `Evicted`, CPU throttling, scheduling failures
- [[Dependency & Import Errors]] — `ImagePullBackOff` (wrong image name, registry auth)
- [[Permission & Authorization Errors]] — RBAC `Forbidden`, NetworkPolicy blocking traffic
- [[Configuration & Environment Errors]] — ConfigMap/Secret not found, wrong namespace
- [[Connection & Network Errors]] — Service DNS not resolving, pod not ready

## Debug Flow
1. `kubectl get pods` — see pod status
2. `kubectl describe pod <name>` — see events and conditions
3. `kubectl logs <pod>` — see container output
4. `kubectl logs <pod> --previous` — see crashed container logs
5. `kubectl get events --sort-by=.metadata.creationTimestamp` — cluster events
6. `kubectl exec -it <pod> -- /bin/sh` — shell into pod

## Common Gotchas
- Liveness probe too aggressive = pod keeps restarting
- Resource limits too tight = OOMKilled or CPU throttling
- DNS between namespaces: `service.namespace.svc.cluster.local`
- RBAC is deny-by-default — must explicitly grant permissions

## Related
- [[Docker]] — container runtime
- [[Terraform]] — IaC for K8s infrastructure
- [[AWS]], [[GCP]], [[Azure]] — managed K8s (EKS, GKE, AKS)
- [[MOC — Cloud & Infrastructure]]

## My Notes

