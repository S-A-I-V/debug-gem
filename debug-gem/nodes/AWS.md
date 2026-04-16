---
tags: [cloud, infrastructure, platform]
aliases: [aws, amazon-web-services]
---
# AWS (Amazon Web Services)

Largest cloud platform. Services span compute, storage, networking, databases, AI, and more.

## Known For These Error Patterns
- [[Permission & Authorization Errors]] — IAM is powerful but complex. `AccessDeniedException` is the most common AWS error.
- [[Resource Exhaustion Errors]] — Service quotas, Lambda timeouts, throttling
- [[Connection & Network Errors]] — VPC networking (security groups, NACLs, NAT gateways)
- [[Configuration & Environment Errors]] — Wrong region, missing env vars, credential chain confusion

## Key Debug Tools
- CloudWatch Logs — centralized logging
- CloudTrail — API audit trail (find exact permission denial reason)
- X-Ray — distributed tracing
- VPC Flow Logs — network traffic logging
- IAM Policy Simulator — test policies without real API calls

## IAM Debug Flow
1. Who is the caller? (IAM user, role, service)
2. What action? (s3:GetObject, ec2:RunInstances)
3. What resource? (arn:aws:s3:::bucket/key)
4. Check: User policy → Role policy → Resource policy → SCP → Permission boundary

## Related
- [[GCP]], [[Azure]] — competing cloud platforms
- [[Terraform]] — IaC for AWS
- [[Kubernetes]] — EKS on AWS
- [[Lambda]] — serverless compute
- [[MOC — Cloud & Infrastructure]]

## My Notes

