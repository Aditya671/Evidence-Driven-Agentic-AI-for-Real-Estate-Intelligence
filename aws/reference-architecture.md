# AWS Reference Architecture

## Purpose

This is the AWS implementation of the cloud-neutral evidence-driven real-estate intelligence architecture.

The design intentionally uses current AWS capabilities while preserving the application contracts defined in the cloud-neutral track.

## Reference flow

```text
User / API / Batch
      |
      v
API / Application Layer
      |
      v
AgentCore Runtime or Container Runtime
      |
      +--------------------+
      |                    |
      v                    v
Source Discovery      Structured Data
      |                    |
      v                    v
S3 + Metadata       PostgreSQL/Aurora
      |
      v
Textract / Document Processing
      |
      v
OpenSearch / Bedrock Knowledge Base
      |
      v
Scoped Evidence Retrieval
      |
      v
Extraction + Validation
      |
      v
Enrichment + Output
      |
      v
S3 / Database / API
      |
      v
CloudWatch + OpenTelemetry
```

## 1. Object storage

Use Amazon S3 as the document system of record.

Recommended prefixes:

```text
raw/
processed/
evidence/
outputs/
archive/
```

Use application metadata for:

- asset ID;
- document ID;
- document version;
- checksum;
- document type;
- period;
- source.

S3 event notifications can trigger downstream processing; they are at-least-once, so ingestion must be idempotent.

## 2. Document analysis

Amazon Textract can analyze text, forms, tables, queries, signatures, and layout.

For asynchronous multi-page processing, `StartDocumentAnalysis` can process documents stored in S3 and publish completion notifications.

Use Textract as a document-understanding capability, not as the owner of the business extraction rules.

## 3. Search

Amazon OpenSearch Service / OpenSearch Serverless can provide full-text and vector search.

A practical implementation is:

```text
S3 processed representation
       |
       v
OpenSearch ingestion/indexing
       |
       +--> metadata filters
       +--> keyword retrieval
       +--> semantic/vector retrieval
```

For the project, metadata-scoped retrieval should remain the primary control.

## 4. Bedrock

Amazon Bedrock Knowledge Bases can provide managed retrieval and generation capabilities.

However, the project should preserve access to the underlying retrieval stage when evidence control matters.

Prefer:

```text
Retrieve
  -> inspect evidence
  -> extract
```

over an opaque:

```text
RetrieveAndGenerate
```

when the application requires explicit evidence validation and stage-level observability.

Bedrock also supports structured-data query generation through Knowledge Bases for supported data stores.

## 5. Agent runtime

For a managed AWS agent runtime, use Amazon Bedrock AgentCore rather than building the architecture around the older Bedrock Agents Classic abstraction.

AgentCore is framework/model agnostic and can work with agent frameworks and external model providers.

The project can alternatively run the orchestrator in ECS/EKS/Lambda-style application infrastructure when explicit control is more important than managed agent runtime features.

## 6. Workflow

Use AWS Step Functions for durable workflow orchestration when the workflow contains:

- retries;
- branching;
- human review;
- asynchronous processing;
- batch execution;
- failure recovery.

Keep the business state machine separate from the LLM's internal reasoning loop.

## 7. Observability

Use OpenTelemetry in the application layer.

Export telemetry to CloudWatch or another supported backend.

Track:

- run ID;
- request ID;
- agent session;
- tool name;
- retrieval strategy;
- document ID;
- evidence ID;
- extraction rule version;
- model configuration;
- latency;
- token/cost metadata where available;
- error category.

AgentCore also provides agent observability and emits standardized telemetry that can integrate with broader observability systems.

## 8. Security

Use IAM roles and workload identities.

Apply least privilege separately to:

- ingestion;
- parsing;
- retrieval;
- model inference;
- output;
- monitoring.

Do not give the agent a broad role that can directly modify source documents.

## 9. AWS-specific failure boundaries

```text
S3 failure
  -> ingestion retry

Textract failure
  -> processing retry / dead-letter

Search failure
  -> retrieval retry / fallback

Model failure
  -> bounded model retry / alternate provider

Extraction invalid
  -> validation repair / review

Evidence conflict
  -> business-rule resolution / human review
```

## 10. Why this is still cloud-neutral

Only the implementation layer knows that:

- object storage is S3;
- document intelligence is Textract;
- search is OpenSearch;
- model platform is Bedrock;
- workflow is Step Functions;
- managed agent runtime is AgentCore;
- observability backend is CloudWatch.

The domain and evidence contracts do not.
