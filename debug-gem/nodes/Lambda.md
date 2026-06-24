---
tags: [cloud, serverless, aws, compute]
aliases: [lambda, aws-lambda, serverless]
---
# Lambda (AWS Lambda)

Serverless compute service by [[AWS]]. Run code without managing servers. Pay per execution. Supports Node.js, Python, Java, Go, .NET, Ruby.

## Known For These Error Patterns
- [[Resource Exhaustion Errors]] — Timeout (max 15 min), memory limit, `/tmp` storage full (512MB default), concurrency throttling
- [[State & Lifecycle Errors]] — Cold starts, execution context reuse, handler invoked before dependencies ready
- [[Connection & Network Errors]] — VPC timeout (NAT Gateway needed for internet), ENI limit
- [[Permission & Authorization Errors]] — `AccessDeniedException` (execution role missing permissions)
- [[Configuration & Environment Errors]] — Missing env vars, wrong handler path, wrong runtime version

## Common Errors
- `Task timed out after X seconds` — Function hit timeout limit. Increase timeout or optimize code. If calling external service, check VPC/network.
- `Runtime.ImportModuleError` / `Cannot find module` — [[Dependency & Import Errors]]: Wrong handler path, missing dependency in deployment package, layer not attached.
- `ENOMEM` / OOM — Memory exhausted. Increase memory allocation (also increases CPU proportionally).
- `TooManyRequestsException` — Concurrency limit reached. Request reserved concurrency or implement retry with backoff.
- `AccessDeniedException` — Execution role missing required IAM permissions for the AWS service being called.
- `ECONNREFUSED` / `ETIMEDOUT` — Lambda in VPC can't reach internet (needs NAT Gateway) or can't reach AWS services (needs VPC endpoint).

## Cold Start Gotchas
- Cold start = new execution environment provisioned. Adds 100ms–10s+ depending on runtime/size.
- Java/.NET have the worst cold starts (JVM/CLR startup). Node.js/Python are fastest.
- VPC adds cold start latency (ENI attachment)
- Provisioned Concurrency eliminates cold starts (but costs more)
- Keep deployment package small — large packages = slower cold starts
- Initialize SDK clients OUTSIDE the handler (reused across invocations)

## Execution Context Reuse
```
// GOOD — initialized once, reused across invocations
const dbClient = new DynamoDB();

exports.handler = async (event) => {
  // GOOD — uses existing connection
  return dbClient.get(params);
};
```
- Connections, SDK clients, and global variables persist between invocations
- `/tmp` directory persists between invocations (but not guaranteed)
- DON'T rely on reuse for correctness — treat each invocation as potentially fresh

## Common Gotchas
- **Timeout vs downstream timeout** — if Lambda timeout is 30s but HTTP client timeout is 60s, Lambda dies mid-request
- **Synchronous vs async invocation** — sync returns response, async puts event in queue (different retry behavior)
- **DLQ/Destinations** — failed async invocations go to Dead Letter Queue after retries
- **Memory = CPU** — increasing memory also increases CPU allocation proportionally. Sometimes increasing memory makes function cheaper (runs faster).
- **Environment variables** — limited to 4KB total. Use Parameter Store/Secrets Manager for more.
- **Layers** — shared dependencies across functions. Max 5 layers, 250MB unzipped total.

## Debug
- CloudWatch Logs — all `console.log`/`print` goes here
- X-Ray — distributed tracing for latency analysis
- `aws lambda invoke --log-type Tail` — invoke and get logs in response
- Test locally with SAM CLI: `sam local invoke`

## Related
- [[AWS]] — Lambda is an AWS service
- [[DynamoDB]] — common Lambda companion (serverless DB)
- [[Kubernetes]] — alternative compute (server-based)
- [[Node.js]], [[Python]], [[Java]] — common Lambda runtimes
- [[MOC — Cloud & Infrastructure]]

## My Notes

