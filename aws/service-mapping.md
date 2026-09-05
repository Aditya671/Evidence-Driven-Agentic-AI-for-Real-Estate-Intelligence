# AWS Service Mapping

## Capability-to-service mapping

| Cloud-neutral capability | AWS implementation | Role |
|---|---|---|
| Object store | Amazon S3 | Source and artifact storage |
| Eventing | S3 Events / EventBridge | Ingestion triggers |
| Queue | SQS | Back-pressure and retry isolation |
| OCR/document analysis | Amazon Textract | Text/layout/table/form analysis |
| Search | Amazon OpenSearch Service | Keyword, metadata, vector retrieval |
| Managed RAG | Bedrock Knowledge Bases | Optional managed retrieval |
| Model inference | Amazon Bedrock | Foundation model access |
| Agent runtime | Bedrock AgentCore | Managed agent hosting/operations |
| Durable workflow | Step Functions | Workflow state, retry, branching |
| Relational state | Aurora PostgreSQL / RDS PostgreSQL | Run registry, mappings |
| Containers | ECS/EKS/ECR | Custom services and workers |
| Secrets | Secrets Manager | Secret storage |
| Encryption | KMS | Key management |
| Identity | IAM | Least privilege |
| Observability | CloudWatch | Logs, metrics, dashboards |
| Portable telemetry | OpenTelemetry | Cross-backend instrumentation |

## Selection guidance

### S3

Use for:

- source documents;
- processed artifacts;
- evidence snapshots;
- exports;
- archives.

### Textract

Use when the document requires:

- text;
- tables;
- forms;
- layout;
- query responses.

Do not assume OCR output alone is sufficient for financial table interpretation.

### OpenSearch

Use when the application needs:

- metadata filters;
- keyword search;
- semantic/vector retrieval;
- custom ranking.

### Bedrock Knowledge Bases

Useful when managed RAG reduces implementation burden.

For this architecture, retain a path to explicit `Retrieve` operations so the application can inspect evidence before generation.

### AgentCore

Use when managed agent hosting, tool connectivity, identity, memory, and observability materially reduce operational burden.

Do not make AgentCore mandatory for the domain architecture.

### Step Functions

Use for durable, visible business workflows.

Do not put every LLM thought into Step Functions. Orchestrate business stages and recovery boundaries.

## Current AWS lifecycle consideration

AWS documentation now distinguishes Bedrock Agents Classic from newer AgentCore capabilities. New AWS documentation should prefer AgentCore for new managed-agent deployments rather than designing around Agents Classic.

## Portability warning

An AWS service is an implementation choice, not the architecture itself.
