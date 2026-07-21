---
title: "Secure Multi-Tenant RAG with Amazon Bedrock"
date: 2026-07-21
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---
## Secure Multi-Tenant RAG: Document Access Control with Amazon Bedrock and Verified Permissions

## 1. Role and Research Context
Large organizations building internal GenAI apps often need to:
- Restrict each department to its own documents.
- Allow executives cross-department access.
- Avoid provisioning a separate Knowledge Base per team (costly and hard to operate).

RAG connects foundation models to enterprise data in near real time. This post extends earlier metadata-filtering patterns by **moving authorization logic out of application code** into Cedar policies managed by Amazon Verified Permissions — so access rules can change in minutes without redeploying.

## 2. Technical Highlights

### Isolation model
- This pattern provides **logical (filter-level) isolation**, not hard IAM-enforced isolation between SaaS customers.
- It fits **intra-tenant** use cases: departments/teams/roles inside one organization.
- It does not replace a dedicated Knowledge Base per tenant when hard compliance boundaries are required between organizations.

### Defense-in-depth with two independent layers
| Layer | Component | Decision | On deny |
| ----- | --------- | -------- | ------- |
| **Layer 1** | Lambda Authorizer on API Gateway | Can the user invoke the API? | Return 403 |
| **Layer 2** | Middleware Lambda | Which department tags are allowed? | Empty result set |
| **KB filter** | Amazon Bedrock Knowledge Bases | Apply metadata filter before retrieval | Exclude unauthorized docs |
| **Guardrails** | Amazon Bedrock Guardrails | Is the response grounded? | Block/modify response |

Both layers call Verified Permissions independently and **fail closed (deny-by-default)**. If Layer 1 is bypassed, Layer 2 still enforces document isolation.

### Ingestion pipeline with metadata tagging
1. Upload documents to S3 under department prefixes (`docs/dept-a/report.pdf`).
2. EventBridge → SQS → Lambda writes a `.metadata.json` sidecar with a `department` attribute.
3. A scheduled Lambda (every 5 minutes) calls `StartIngestionJob`.
4. Documents without sidecars are excluded from ingestion to avoid filter bypass.

### Query flow
1. The user sends a query with a Cognito JWT containing `cognito:groups`.
2. CloudFront + AWS WAF enforce edge controls.
3. The Lambda Authorizer evaluates Cedar policies (Layer 1).
4. Middleware Lambda asks Verified Permissions, builds a metadata filter, and calls `RetrieveAndGenerate`.
5. The foundation model only receives context from authorized documents.

Example Cedar policies: `dept-a` can query only its own Knowledge Base resources; `dept-c` (leadership) can query across departments.

## 3. AWS Services in the Architecture
- **Amazon Bedrock Knowledge Bases** (RAG + metadata filtering)
- **Amazon Verified Permissions** + Cedar policies
- **Amazon Cognito** (JWT + group claims)
- **Amazon API Gateway** + Lambda Authorizer
- **AWS Lambda** (tagging, authorizer, middleware)
- **Amazon S3, EventBridge, SQS**
- **Amazon CloudFront + AWS WAF**
- **Amazon Bedrock Guardrails**
- **Amazon DynamoDB** (session context)
- **AWS CloudTrail / CloudWatch** (audit & monitoring)

## 4. Practical Value
- **One shared Knowledge Base**: lower cost and operational overhead than one KB per department.
- **Policy updates without redeploy**: change Cedar policies in the console; the next request applies them.
- **Built-in audit trail**: CloudTrail records every `IsAuthorized` decision.
- **Easy expansion**: adding a department means adding a policy and tagging documents — no new infrastructure stack.

## 5. Limitations and Implementation Notes
- This is **filter-level isolation**: a fail-open middleware bug could expose documents — design must stay deny-by-default.
- A short race exists between upload and sidecar creation; safeguard checks before ingestion are required.
- Do not use this pattern as a substitute for hard tenant isolation across customers/organizations.
- Protect metadata sidecars (S3 Versioning, restricted `PutObject`, CloudTrail/CloudWatch alerts).
- Billable resources include Bedrock, Lambda, Cognito, Verified Permissions, and more — clean up after labs.

## Conclusion
Secure Multi-Tenant RAG shows how enterprises can serve many departments from one RAG application while keeping logical document isolation, updating access flexibly, and retaining full auditability. Combining Amazon Bedrock Knowledge Bases with Amazon Verified Permissions is a practical approach for internal GenAI that needs fine-grained, secure access control.

*Source: AWS Architecture Blog*
*Original document: [Secure multi-tenant RAG with Amazon Bedrock and Verified Permissions](https://aws.amazon.com/blogs/architecture/secure-multi-tenant-rag-with-amazon-bedrock-and-verified-permissions/)*
