# MOC — Cloud & Infrastructure

> Error patterns specific to cloud platforms, containers, orchestration, and infrastructure-as-code. This is where [[The Environment Delta]] and [[Permission & Authorization Errors]] dominate.

---

## [[AWS]]

### IAM & Permissions
- `AccessDeniedException` → [[Permission & Authorization Errors]]: IAM policy doesn't allow the action. Check: user policies, role policies, resource policies, SCPs, permission boundaries.
- `UnauthorizedAccess` → [[Permission & Authorization Errors]]: Similar to above, often from API Gateway or service-level auth.
- `ExpiredTokenException` → [[State & Lifecycle Errors]]: STS temporary credentials expired. Re-assume role.
- `InvalidIdentityToken` → [[Configuration & Environment Errors]]: OIDC/SAML token invalid for role assumption.
- `MalformedPolicyDocument` → [[Syntax & Parse Errors]]: IAM policy JSON is invalid.
- `The security token included in the request is expired` → [[State & Lifecycle Errors]]: Refresh credentials.
- `User: arn:aws:iam::X is not authorized to perform: Y on resource: Z` → [[Permission & Authorization Errors]]: Exact denial message. Check the policy chain.
- `An error occurred (AccessDenied) when calling the AssumeRole operation` → [[Permission & Authorization Errors]]: Trust policy doesn't allow the caller.

### EC2 & Networking
- `InsufficientInstanceCapacity` → [[Resource Exhaustion Errors]]: No capacity in AZ. Try different AZ or instance type.
- `InstanceLimitExceeded` → [[Resource Exhaustion Errors]]: Account limit reached. Request increase.
- `VPCLimitExceeded` → [[Resource Exhaustion Errors]]: Too many VPCs in region.
- `InvalidParameterValue: Security group X does not exist in VPC Y` → [[Configuration & Environment Errors]]: SG in wrong VPC.
- `NetworkInterfaceNotFound` → [[Configuration & Environment Errors]]: ENI doesn't exist or wrong region.
- `Client.InternalError: Client error on launch` → Various: Often AMI issue, EBS issue, or instance profile issue.
- `i]nstance is not in a VPC` → [[Configuration & Environment Errors]]: EC2-Classic vs VPC confusion.
- Timeout connecting to EC2 → [[Connection & Network Errors]]: Check security group inbound rules, NACL, route table, internet gateway/NAT gateway.

### Lambda
- `Task timed out after X seconds` → [[Resource Exhaustion Errors]]: Function execution exceeded timeout. Increase timeout or optimize.
- `Runtime.ImportModuleError` → [[Dependency & Import Errors]]: Module not in deployment package or layer.
- `Runtime.HandlerNotFound` → [[Configuration & Environment Errors]]: Handler path wrong in function config.
- `ENOMEM` → [[Resource Exhaustion Errors]]: Memory limit too low. Increase memory (also increases CPU).
- `TooManyRequestsException` → [[Resource Exhaustion Errors]]: Concurrency limit reached. Request increase or use reserved concurrency.
- `InvalidParameterValueException: Unzipped size must be smaller than X bytes` → [[Resource Exhaustion Errors]]: Deployment package too large. Use layers or container image.
- `Runtime exited with error: signal: killed` → [[Resource Exhaustion Errors]]: OOM killed. Increase memory.
- Cold start latency → Not an error but a performance issue. Use provisioned concurrency, smaller packages, or SnapStart (Java).

### S3
- `AccessDenied` → [[Permission & Authorization Errors]]: Check bucket policy + IAM policy + ACL + block public access settings.
- `NoSuchBucket` → [[Configuration & Environment Errors]]: Bucket doesn't exist or wrong region.
- `NoSuchKey` → [[Configuration & Environment Errors]]: Object key doesn't exist. Check exact path (case-sensitive).
- `SlowDown` → [[Resource Exhaustion Errors]]: Request rate too high for prefix. Distribute keys across prefixes.
- `EntityTooLarge` → [[Resource Exhaustion Errors]]: Single PUT limited to 5GB. Use multipart upload.
- `InvalidBucketName` → [[Syntax & Parse Errors]]: Bucket name doesn't meet naming rules.
- `PermanentRedirect` → [[Configuration & Environment Errors]]: Using wrong region endpoint for bucket.
- Eventual consistency → [[Concurrency & Race Condition Errors]]: S3 is now strongly consistent for all operations (since Dec 2020), but CloudFront caching can still show stale data.

### DynamoDB
- `ProvisionedThroughputExceededException` → [[Resource Exhaustion Errors]]: RCU/WCU exceeded. Use on-demand or increase capacity.
- `ConditionalCheckFailedException` → [[Concurrency & Race Condition Errors]]: Condition expression not met (optimistic locking).
- `TransactionConflictException` → [[Concurrency & Race Condition Errors]]: Transaction conflicts with another.
- `ValidationException: Item size has exceeded the maximum allowed size` → [[Resource Exhaustion Errors]]: Item > 400KB.
- `ResourceNotFoundException` → [[Configuration & Environment Errors]]: Table doesn't exist.
- `ItemCollectionSizeLimitExceededException` → [[Resource Exhaustion Errors]]: Local secondary index partition > 10GB.
- Hot partition → [[Resource Exhaustion Errors]]: Uneven key distribution. Redesign partition key.

### RDS / Aurora
- `Cannot connect to RDS instance` → [[Connection & Network Errors]]: Check security group, VPC, public accessibility setting, SSL mode.
- `Too many connections` → [[Resource Exhaustion Errors]]: Use RDS Proxy or connection pooling.
- `Storage full` → [[Resource Exhaustion Errors]]: Enable storage autoscaling.
- `Replication lag` → [[Concurrency & Race Condition Errors]]: Read replica behind primary. Check replica lag metric.

### SQS / SNS / EventBridge
- `MessageNotInflight` → [[State & Lifecycle Errors]]: Message visibility timeout expired.
- `OverLimit` → [[Resource Exhaustion Errors]]: Too many inflight messages.
- `InvalidParameterValue` → [[Type & Casting Errors]]: Message attribute type mismatch.
- Dead letter queue messages → Messages that failed processing. Check DLQ for debugging.

### CloudFormation / CDK
- `CREATE_FAILED` → Various: Read the status reason. Often [[Permission & Authorization Errors]] or [[Configuration & Environment Errors]].
- `UPDATE_ROLLBACK_FAILED` → [[State & Lifecycle Errors]]: Stack in broken state. May need manual intervention.
- `Circular dependency between resources` → [[Dependency & Import Errors]]: Resources reference each other.
- `Resource limit exceeded` → [[Resource Exhaustion Errors]]: Stack resource limit (500 resources).
- `Template format error` → [[Syntax & Parse Errors]]: Invalid CloudFormation template.

### General AWS Debug
- **CloudWatch Logs**: First place to check for Lambda, ECS, API Gateway errors.
- **CloudTrail**: Audit log for all API calls. Find exact permission denial details.
- **X-Ray**: Distributed tracing for request flow.
- **VPC Flow Logs**: Network-level traffic logging. Find blocked connections.
- **IAM Policy Simulator**: Test policies without making real API calls.
- **AWS Config**: Track configuration changes over time.

---

## [[GCP]]

### Common Errors
- `PERMISSION_DENIED` → [[Permission & Authorization Errors]]: IAM role missing. Check project-level and resource-level IAM.
- `NOT_FOUND` → [[Configuration & Environment Errors]]: Resource doesn't exist or wrong project.
- `RESOURCE_EXHAUSTED` → [[Resource Exhaustion Errors]]: Quota exceeded. Check quotas page.
- `UNAUTHENTICATED` → [[Permission & Authorization Errors]]: No valid credentials. Check service account key, ADC.
- `ALREADY_EXISTS` → [[Concurrency & Race Condition Errors]] or [[State & Lifecycle Errors]]: Resource already created.
- `FAILED_PRECONDITION` → [[State & Lifecycle Errors]]: Resource in wrong state for operation.
- `DEADLINE_EXCEEDED` → [[Connection & Network Errors]]: Operation timed out.
- `UNAVAILABLE` → [[Connection & Network Errors]]: Service temporarily unavailable.
- `INVALID_ARGUMENT` → [[Type & Casting Errors]]: Invalid request parameter.
- `INTERNAL` → Server-side error. Retry with backoff.
- `DATA_LOSS` → [[Serialization & Encoding Errors]]: Unrecoverable data loss or corruption.

### GCP-Specific Traps
- **Application Default Credentials (ADC)**: `gcloud auth application-default login` for local dev. Service account key for production. Don't commit keys.
- **Project scoping**: Resources are project-scoped. Wrong project = resource not found.
- **Cloud Run cold starts**: Similar to Lambda. Use min instances for latency-sensitive services.
- **Firestore vs Datastore mode**: Can't switch after creation. Different APIs and behavior.
- **Cloud Functions gen1 vs gen2**: Different runtimes, limits, and behavior.
- **VPC Service Controls**: Can block API access even with correct IAM. Check perimeter configuration.

### Debug Tools
- Cloud Logging (Stackdriver) — centralized logging.
- Error Reporting — automatic error grouping and alerting.
- Cloud Trace — distributed tracing.
- Cloud Profiler — continuous profiling.
- `gcloud` CLI — `gcloud logging read`, `gcloud compute ssh`, etc.

---

## [[Azure]]

### Common Errors
- `AuthorizationFailed` → [[Permission & Authorization Errors]]: RBAC role missing.
- `ResourceNotFound` → [[Configuration & Environment Errors]]: Resource doesn't exist or wrong resource group.
- `ResourceGroupNotFound` → [[Configuration & Environment Errors]]: Resource group doesn't exist.
- `SubscriptionNotFound` → [[Configuration & Environment Errors]]: Wrong subscription ID.
- `QuotaExceeded` → [[Resource Exhaustion Errors]]: Subscription quota limit.
- `Conflict` → [[Concurrency & Race Condition Errors]]: Resource state conflict.
- `DeploymentFailed` → Various: Check deployment operation details.
- `InvalidTemplate` → [[Syntax & Parse Errors]]: ARM/Bicep template error.

### Azure-Specific Traps
- **Subscription/Resource Group scoping**: Resources are scoped to subscription + resource group. Wrong scope = not found.
- **Managed Identity**: System-assigned vs user-assigned. Must be configured on the resource AND granted permissions.
- **VNET Integration**: App Service VNET integration has specific requirements and limitations.
- **Azure AD vs Azure RBAC**: Different permission systems. AD for identity, RBAC for resource access.

### Debug Tools
- Azure Monitor — metrics and logs.
- Application Insights — APM and distributed tracing.
- Log Analytics — KQL queries on logs.
- Azure CLI — `az monitor`, `az resource`, etc.
- Azure Portal Activity Log — audit trail.

---

## [[Docker]]

### Common Errors
- `OOMKilled` → [[Resource Exhaustion Errors]]: Container exceeded memory limit. Increase limit or fix memory usage.
- `Error response from daemon: Conflict. The container name "X" is already in use` → [[State & Lifecycle Errors]]: Remove old container first.
- `Error response from daemon: driver failed programming external connectivity: port is already allocated` → [[Resource Exhaustion Errors]]: Port already in use.
- `COPY failed: file not found in build context` → [[Configuration & Environment Errors]]: File not in build context or excluded by `.dockerignore`.
- `Error response from daemon: pull access denied` → [[Permission & Authorization Errors]]: Need `docker login` or image doesn't exist.
- `Error response from daemon: manifest for X not found` → [[Configuration & Environment Errors]]: Image tag doesn't exist.
- `standard_init_linux.go: exec user process caused: exec format error` → [[Configuration & Environment Errors]]: Architecture mismatch (ARM image on x86 or vice versa).
- `standard_init_linux.go: exec user process caused: no such file or directory` → [[Configuration & Environment Errors]]: Entrypoint binary not found. Check ENTRYPOINT/CMD path.
- `Error: No such container: X` → [[State & Lifecycle Errors]]: Container doesn't exist.
- `Cannot connect to the Docker daemon` → [[Connection & Network Errors]]: Docker daemon not running. Start Docker Desktop or `systemctl start docker`.
- `Got permission denied while trying to connect to the Docker daemon socket` → [[Permission & Authorization Errors]]: User not in `docker` group. `sudo usermod -aG docker $USER`.
- `network X not found` → [[Configuration & Environment Errors]]: Docker network doesn't exist.
- `Error response from daemon: Conflict. The network X has active endpoints` → [[State & Lifecycle Errors]]: Can't remove network with connected containers.
- `HEALTHCHECK failing` → [[State & Lifecycle Errors]]: Container unhealthy. Check health check command and application state.
- `exited with code 137` → [[Resource Exhaustion Errors]]: OOM killed (128 + 9 = SIGKILL).
- `exited with code 1` → Application error. Check container logs.
- `exited with code 126` → [[Permission & Authorization Errors]]: Command not executable.
- `exited with code 127` → [[Dependency & Import Errors]]: Command not found.

### Docker-Specific Traps
- **Layer caching**: Changing a line invalidates all subsequent layers. Order Dockerfile instructions from least to most frequently changing.
- **`.dockerignore`**: Missing `.dockerignore` sends entire directory as build context (including `node_modules`, `.git`).
- **Multi-stage builds**: Final stage must COPY artifacts from build stage. Missing COPY = missing files.
- **PID 1 signal handling**: Container's PID 1 doesn't get default signal handlers. Use `tini` or `dumb-init` as entrypoint.
- **Slim/Alpine images**: Missing system libraries. `python:slim` lacks `gcc`, `libffi`. Alpine uses `musl` not `glibc`.
- **Root vs non-root**: Running as root is a security risk. Use `USER` directive. But some operations need root during build.
- **Volume permissions**: Host volume mounted with different UID/GID than container user. Use `--user` flag or fix permissions.
- **DNS resolution**: Containers use Docker's embedded DNS. Custom networks get service discovery. Default bridge doesn't.
- **Build args vs env vars**: `ARG` is build-time only. `ENV` persists in the image. Don't put secrets in either (they're in layer history).

### Debug Commands
- `docker logs <container>` — view container output.
- `docker logs -f <container>` — follow/tail logs.
- `docker inspect <container>` — full container metadata.
- `docker stats` — live resource usage.
- `docker exec -it <container> /bin/sh` — shell into running container.
- `docker history <image>` — see image layers.
- `docker system df` — disk usage.
- `docker system prune` — clean up unused resources.
- `docker network inspect <network>` — network details.
- `docker compose logs` — logs for all compose services.

---

## [[Kubernetes]]

### Pod Errors
- `CrashLoopBackOff` → [[State & Lifecycle Errors]]: Container keeps crashing and restarting. Check `kubectl logs` for the crash reason.
- `ImagePullBackOff` / `ErrImagePull` → [[Dependency & Import Errors]] or [[Permission & Authorization Errors]]: Can't pull image. Wrong image name, tag doesn't exist, or registry auth missing.
- `OOMKilled` → [[Resource Exhaustion Errors]]: Container exceeded memory limit. Increase `resources.limits.memory` or fix memory usage.
- `Evicted` → [[Resource Exhaustion Errors]]: Node under resource pressure. Pod evicted based on QoS class.
- `Pending` (stuck) → [[Resource Exhaustion Errors]]: No node has enough resources. Check `kubectl describe pod` for scheduling failures.
- `ContainerCreating` (stuck) → [[Configuration & Environment Errors]]: Usually waiting for volume mount, ConfigMap, or Secret.
- `CreateContainerConfigError` → [[Configuration & Environment Errors]]: ConfigMap or Secret referenced but doesn't exist.
- `RunContainerError` → [[Configuration & Environment Errors]]: Container can't start. Check entrypoint, command, security context.
- `Error: container X is not valid for pod Y` → [[Configuration & Environment Errors]]: Container spec invalid.
- `Init:Error` / `Init:CrashLoopBackOff` → Init container failing. Check init container logs.
- `PostStartHookError` → [[State & Lifecycle Errors]]: Post-start hook failed.
- `Terminating` (stuck) → [[State & Lifecycle Errors]]: Finalizer blocking deletion. Check finalizers, force delete if needed.

### Service & Networking
- `Service has no endpoints` → [[Configuration & Environment Errors]]: Label selector doesn't match any pods. Check labels.
- `Connection refused` to Service → [[Connection & Network Errors]]: Pod not ready, wrong port, or service misconfigured.
- `DNS resolution failed` → [[Connection & Network Errors]]: CoreDNS not running or misconfigured. Check `kube-system` pods.
- `NetworkPolicy blocking traffic` → [[Permission & Authorization Errors]]: Network policy denying pod-to-pod communication.
- `Ingress returning 404/502/503` → [[Configuration & Environment Errors]]: Ingress rules don't match, backend service not ready, or ingress controller misconfigured.
- `TLS certificate error on Ingress` → [[Connection & Network Errors]]: Secret with TLS cert not found or cert expired.

### RBAC & Security
- `Error from server (Forbidden)` → [[Permission & Authorization Errors]]: RBAC role/rolebinding missing.
- `User "system:serviceaccount:X:Y" cannot Z` → [[Permission & Authorization Errors]]: ServiceAccount lacks permission.
- `PodSecurityPolicy` / `Pod Security Standards` violation → [[Permission & Authorization Errors]]: Pod spec violates security policy.
- `seccomp/AppArmor` blocking → [[Permission & Authorization Errors]]: Security profile blocking syscall.

### Storage
- `PVC Pending` → [[Resource Exhaustion Errors]] or [[Configuration & Environment Errors]]: No PV available, StorageClass misconfigured, or provisioner failing.
- `FailedMount` → [[Configuration & Environment Errors]]: Volume can't be mounted. Check PV/PVC, node affinity, access modes.
- `FailedAttachVolume` → [[Resource Exhaustion Errors]]: Max volumes per node reached, or volume attached to another node.
- `ReadOnlyFilesystem` → [[Permission & Authorization Errors]]: Volume mounted read-only or security context enforcing read-only root.

### Deployment & Scaling
- `FailedCreate` → Various: Check ReplicaSet events for details.
- `ProgressDeadlineExceeded` → [[State & Lifecycle Errors]]: Deployment not progressing. New pods failing to become ready.
- `HPA unable to calculate` → [[Configuration & Environment Errors]]: Metrics server not installed or metrics not available.
- `ResourceQuota exceeded` → [[Resource Exhaustion Errors]]: Namespace quota limit reached.
- `LimitRange violation` → [[Configuration & Environment Errors]]: Pod spec doesn't meet LimitRange requirements.

### Helm
- `Error: UPGRADE FAILED: another operation is in progress` → [[Concurrency & Race Condition Errors]]: Previous Helm operation didn't complete. Check `helm history`, rollback if needed.
- `Error: rendered manifests contain a resource that already exists` → [[State & Lifecycle Errors]]: Resource exists outside Helm management.
- `Error: template: X: function "Y" not defined` → [[Syntax & Parse Errors]]: Helm template function error.
- `Error: values don't meet the specifications` → [[Type & Casting Errors]]: Values file doesn't match expected schema.

### Debug Commands
- `kubectl describe pod <pod>` — events, conditions, container status.
- `kubectl logs <pod> [-c container]` — container logs.
- `kubectl logs <pod> --previous` — logs from previous (crashed) container.
- `kubectl get events --sort-by=.metadata.creationTimestamp` — cluster events.
- `kubectl exec -it <pod> -- /bin/sh` — shell into pod.
- `kubectl port-forward <pod> 8080:80` — access pod locally.
- `kubectl top pods` / `kubectl top nodes` — resource usage.
- `kubectl get pods -o wide` — see which node pods are on.
- `kubectl debug <pod>` — ephemeral debug container (K8s 1.23+).
- `kubectl auth can-i <verb> <resource>` — check RBAC permissions.

---

## [[Terraform]]

### Common Errors
- `Error: Cycle: X, Y` → [[Dependency & Import Errors]]: Circular dependency between resources. Use `depends_on` or restructure.
- `Error: Provider requirements cannot be satisfied` → [[Dependency & Import Errors]]: Provider version conflict.
- `Error: Error acquiring the state lock` → [[Concurrency & Race Condition Errors]]: Another Terraform operation in progress. Wait or force-unlock.
- `Error: Backend initialization required` → [[Configuration & Environment Errors]]: Run `terraform init`.
- `Error: Variables not allowed` → [[Syntax & Parse Errors]]: Variables used in wrong context (e.g., backend config).
- `Error: Invalid provider configuration` → [[Configuration & Environment Errors]]: Provider block misconfigured.
- `Error: Unsupported attribute` → [[Type & Casting Errors]]: Attribute doesn't exist on resource type.
- `Error: Unsupported block type` → [[Syntax & Parse Errors]]: Block not valid in this context.
- `Error: Argument or block definition required` → [[Syntax & Parse Errors]]: HCL syntax error.
- `Error: Resource already exists` → [[State & Lifecycle Errors]]: Resource exists but not in state. Import it.
- `Error: Error creating/updating/deleting X: AccessDenied` → [[Permission & Authorization Errors]]: Cloud credentials lack permission.
- `Error: Error creating/updating/deleting X: Conflict` → [[Concurrency & Race Condition Errors]]: Resource modified outside Terraform.
- `Error: Provider produced inconsistent result` → [[State & Lifecycle Errors]]: Provider bug or API inconsistency.
- `Error: Saved plan is stale` → [[State & Lifecycle Errors]]: State changed between plan and apply.

### Terraform-Specific Traps
- **State drift**: Manual changes outside Terraform cause drift. Use `terraform plan` to detect, `terraform import` to reconcile.
- **`count` vs `for_each`**: `count` uses index (fragile), `for_each` uses key (stable). Changing from count to for_each requires state manipulation.
- **Sensitive values in state**: State file contains all values including secrets. Encrypt state backend. Never commit state to git.
- **`depends_on` implicit vs explicit**: Terraform usually infers dependencies. Use explicit `depends_on` only when implicit doesn't work.
- **Module versioning**: Pin module versions. Unpinned modules can break on update.
- **`terraform destroy` ordering**: Destroy happens in reverse dependency order. Some resources can't be destroyed (e.g., non-empty S3 buckets).
- **Backend migration**: Changing backend config requires `terraform init -migrate-state`.
- **Workspace confusion**: Terraform workspaces vs Terraform Cloud workspaces are different concepts.

### Debug
- `terraform plan` — preview changes before applying.
- `terraform state list` — list all resources in state.
- `terraform state show <resource>` — show resource details.
- `terraform import <resource> <id>` — import existing resource into state.
- `TF_LOG=DEBUG terraform plan` — verbose logging.
- `terraform validate` — syntax and configuration validation.
- `terraform fmt` — format HCL files.
- `terraform graph | dot -Tpng > graph.png` — dependency graph visualization.

---

## [[Linux]]

### Common Errors
- `Permission denied` → [[Permission & Authorization Errors]]: Check file permissions (`ls -la`), ownership, SELinux/AppArmor.
- `No such file or directory` → [[Configuration & Environment Errors]]: Path wrong, file doesn't exist, or missing shared library.
- `Command not found` → [[Dependency & Import Errors]]: Binary not installed or not in PATH.
- `No space left on device` → [[Resource Exhaustion Errors]]: Disk full (`df -h`) or inodes exhausted (`df -i`).
- `Cannot allocate memory` → [[Resource Exhaustion Errors]]: RAM exhausted. Check `free -m`, look for memory leaks.
- `Too many open files` → [[Resource Exhaustion Errors]]: File descriptor limit. Check `ulimit -n`, increase with `ulimit -n 65536`.
- `Connection refused` → [[Connection & Network Errors]]: Nothing listening on port. Check `ss -tlnp`.
- `Connection timed out` → [[Connection & Network Errors]]: Firewall dropping packets or host unreachable.
- `Name or service not known` → [[Connection & Network Errors]]: DNS resolution failed. Check `/etc/resolv.conf`.
- `Killed` → [[Resource Exhaustion Errors]]: OOM killer terminated process. Check `dmesg` for OOM messages.
- `Segmentation fault` → [[Offset & Boundary Errors]]: Invalid memory access. Debug with GDB, core dump.
- `Bus error` → [[Offset & Boundary Errors]]: Misaligned memory access or memory-mapped file issue.
- `Broken pipe` → [[Connection & Network Errors]]: Writing to pipe/socket whose reader closed.
- `Resource temporarily unavailable` → [[Resource Exhaustion Errors]]: Non-blocking I/O would block, or process limit reached.
- `Operation not permitted` → [[Permission & Authorization Errors]]: Need root/capability, or SELinux blocking.
- `Text file busy` → [[State & Lifecycle Errors]]: Trying to write to a running executable.
- `Device or resource busy` → [[State & Lifecycle Errors]]: Resource in use (mounted filesystem, open file).
- `Read-only file system` → [[Configuration & Environment Errors]]: Filesystem mounted read-only (disk error, container security).
- `Stale file handle` → [[State & Lifecycle Errors]]: NFS file handle no longer valid.

### Key Debug Tools
- `strace` — trace system calls. `strace -p <pid>` to attach to running process.
- `ltrace` — trace library calls.
- `lsof` — list open files. `lsof -i :8080` for port, `lsof -p <pid>` for process.
- `ss` / `netstat` — socket statistics. `ss -tlnp` for listening TCP ports.
- `top` / `htop` — process resource usage.
- `free -m` — memory usage.
- `df -h` / `df -i` — disk space / inode usage.
- `du -sh *` — directory sizes.
- `dmesg` — kernel messages (OOM kills, hardware errors).
- `journalctl` — systemd journal logs. `journalctl -u <service>` for specific service.
- `systemctl status <service>` — service status and recent logs.
- `ps aux` — all running processes.
- `kill -0 <pid>` — check if process exists.
- `nslookup` / `dig` — DNS resolution.
- `traceroute` / `mtr` — network path tracing.
- `tcpdump` — packet capture. `tcpdump -i any port 8080`.
- `iptables -L` / `nft list ruleset` — firewall rules.
- `ulimit -a` — resource limits for current shell.
- `sysctl -a` — kernel parameters.
- `vmstat` / `iostat` — system performance statistics.
- `sar` — historical system activity.
