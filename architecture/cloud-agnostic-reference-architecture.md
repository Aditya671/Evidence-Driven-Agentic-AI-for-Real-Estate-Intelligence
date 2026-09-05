# Cloud-Agnostic Reference Architecture

## Purpose

This document defines the portable reference architecture for an evidence-driven agentic AI system for real-estate intelligence.

The objective is **not** to hide cloud differences. The objective is to separate:

- business capabilities,
- evidence contracts,
- workflow behavior,
- data contracts,
- security controls,
- observability requirements,

from the infrastructure used to implement them.

That separation allows the same system design to be implemented on AWS, Azure, Google Cloud, a private cloud, Kubernetes, or a hybrid environment without rewriting the business architecture.

## 1. The architecture boundary

The system is best understood as six contracts rather than one cloud stack:

1. **Evidence contract** — what constitutes usable evidence.
2. **Retrieval contract** — how evidence is located and scoped.
3. **Extraction contract** — how a metric is produced from evidence.
4. **Workflow contract** — how the agent decides which capability to invoke.
5. **Output contract** — how raw findings become validated business records.
6. **Operational contract** — how every run is traced, evaluated, recovered, and governed.

A cloud provider supplies implementations for these contracts; it should not redefine them.

## 2. Logical architecture

```text
Business User / API / Batch
          |
          v
+-----------------------------+
| Request + Entity Grounding  |
| asset / metric / time       |
+--------------+--------------+
               |
               v
+-----------------------------+
| Workflow / Agent Runtime    |
| policy + tool selection     |
+--------------+--------------+
               |
       +-------+--------+
       |                |
       v                v
+-------------+   +-------------+
| Source      |   | Structured  |
| Discovery   |   | Data Query  |
+------+------+   +------+------+
       |                 |
       v                 v
+--------------------------------+
| Evidence Retrieval Layer       |
| path -> document -> chunk      |
| semantic fallback -> table     |
+----------------+---------------+
                 |
                 v
+--------------------------------+
| Extraction + Validation        |
| Look -> Extract -> Validate    |
| -> Resolve                     |
+----------------+---------------+
                 |
                 v
+--------------------------------+
| Enrichment / Normalization     |
+----------------+---------------+
                 |
                 v
+--------------------------------+
| Evidence-Bearing Output        |
| JSON / CSV / API / review      |
+----------------+---------------+
                 |
                 v
+--------------------------------+
| Evaluation + Observability     |
| traces / metrics / audit       |
+--------------------------------+
```

## 3. Portable capability model

| Capability | Portable requirement | Typical implementation choices |
|---|---|---|
| Object storage | Durable immutable-ish document store with versioning | S3, Azure Blob, GCS, MinIO |
| Document parsing/OCR | Text, layout, tables, forms, page references | Textract, Azure Document Intelligence, Google Document AI, open-source OCR |
| Search | Keyword + metadata + semantic retrieval | OpenSearch, Azure AI Search, Elasticsearch, PostgreSQL extensions |
| Embeddings | Pluggable embedding provider | Managed model API, self-hosted model |
| LLM inference | Model-provider abstraction | Bedrock, Azure OpenAI, Vertex AI, OpenAI, self-hosted |
| Workflow runtime | Durable orchestration with retries | Step Functions, Durable Functions, Workflows, Temporal |
| Agent runtime | Framework-agnostic execution | Containers, Kubernetes, managed agent runtime |
| Eventing | At-least-once event delivery accepted by design | EventBridge, Service Bus, Pub/Sub, Kafka |
| Queueing | Back-pressure and retry isolation | SQS, Service Bus, Pub/Sub, Kafka |
| Relational state | Run registry, mappings, audit metadata | PostgreSQL, Aurora, Cloud SQL, managed SQL |
| Observability | Logs, metrics, traces with portable schema | OpenTelemetry + provider backend |
| Secrets | Runtime secret retrieval, not source-code storage | Secrets Manager, Key Vault, Secret Manager |
| Identity | Workload identity and least privilege | IAM, Managed Identity, Workload Identity |
| Policy | Explicit authorization and tool constraints | Application policy engine + cloud IAM |
| Artifact registry | Versioned deployable artifacts | ECR, ACR, Artifact Registry |
| Compute | Stateless services and workers | Containers, serverless, Kubernetes, VMs |

## 4. What must remain cloud-neutral

The following must not be embedded in the domain logic:

- cloud-specific resource names;
- provider-specific document IDs as business identifiers;
- provider-specific vector index fields;
- provider-specific LLM prompt semantics;
- provider-specific retry behavior;
- provider-specific telemetry field names;
- provider-specific authentication assumptions.

Instead, define application-level identifiers such as:

```json
{
  "run_id": "run-2026-000184",
  "asset_id": "asset-123",
  "document_id": "doc-456",
  "document_version": "v7",
  "source_record_id": "source-789",
  "evidence_id": "evidence-001",
  "metric_id": "irr",
  "metric_period": "FY2025"
}
```

Cloud-native identifiers may be retained as secondary metadata.

## 5. Retrieval portability

The portable retrieval contract should expose operations such as:

```text
discover_sources(asset, metric, time_context)
retrieve_document(document_id)
retrieve_chunks(document_id, filters)
semantic_fallback(query, filters)
retrieve_table(document_id, page, table_hint)
```

The underlying implementation may use different search engines.

The workflow should never depend on a particular engine's query syntax.

### Recommended retrieval sequence

1. Resolve entity.
2. Resolve metric.
3. Resolve time context.
4. Search candidate source paths.
5. Select a document.
6. Apply document-scoped retrieval.
7. Escalate to semantic fallback only when necessary.
8. Escalate to page/table extraction when flattened text is insufficient.
9. Return evidence objects with provenance.

This is more portable than designing around "RAG" as a single service.

## 6. Document and evidence model

Every extracted value should be capable of answering:

- Which source produced it?
- Which document version?
- Which page?
- Which table or section?
- Which retrieval path?
- Which extraction rule?
- Which run?
- Which model/runtime version?
- Was it validated?
- Was there a conflict?

A minimum evidence record can be represented as:

```json
{
  "value": 18.4,
  "unit": "percent",
  "metric": "irr",
  "status": "valid",
  "source": {
    "document_id": "doc-456",
    "document_version": "v7",
    "page": 42,
    "locator": "table:returns_summary,row:irr"
  },
  "retrieval": {
    "strategy": "document_scoped",
    "query_id": "q-921"
  },
  "provenance": {
    "run_id": "run-2026-000184",
    "extraction_rule_version": "irr-v3"
  }
}
```

## 7. Cloud-neutral extraction pattern

The domain rule remains:

**Look → Extract → Validate → Resolve**

The cloud service may perform OCR, layout analysis, retrieval, or inference, but it does not own the business rule.

Example:

```text
Look:
  locate the return metric in the identified source

Extract:
  capture the numeric value and nearby label

Validate:
  check unit, sign, range, period, and source consistency

Resolve:
  choose the correct value when multiple candidates exist
```

This keeps business correctness portable.

## 8. Failure states

Do not map every failure to "LLM error."

Use explicit states:

- `not_found`
- `not_applicable`
- `invalid`
- `pending`
- `conflict`
- `review_required`
- `system_error`

Provider failures should be separate operational error categories.

## 9. Multi-cloud strategy

A practical portability strategy has three levels.

### Level 1 — Provider-neutral application

Use:

- containerized services,
- PostgreSQL,
- OpenSearch/Elasticsearch,
- object storage abstraction,
- OpenTelemetry,
- provider-neutral model gateway.

This maximizes portability.

### Level 2 — Provider-optimized infrastructure

Use managed services where they materially improve operations.

Examples:

- AWS: S3 + Textract + OpenSearch + Bedrock/AgentCore + Step Functions.
- Azure: Blob Storage + Document Intelligence + AI Search + Azure OpenAI + Functions/Durable Functions.
- Google Cloud: Cloud Storage + Document AI + Vertex AI Search/appropriate search layer + Vertex AI + Workflows.

The application contracts remain unchanged.

### Level 3 — Hybrid

Keep evidence and domain contracts portable while allowing:

- cloud-specific OCR,
- cloud-specific model inference,
- centralized governance,
- private/on-prem data sources.

This is often the best enterprise migration strategy.

## 10. Design rule

The strongest architecture is not "multi-cloud because multi-cloud sounds good."

It is:

> **Cloud-portable where portability protects business continuity; cloud-native where managed capabilities materially improve reliability, security, or cost.**

That distinction prevents unnecessary abstraction while avoiding deep lock-in.

## 11. Standards and interoperability

Observability should use OpenTelemetry where practical because it is explicitly vendor-neutral and supports traces, metrics, and logs across different backends.

AI governance should be expressed as controls and evidence rather than cloud-specific features. NIST AI RMF is a useful cross-sector reference for managing AI risk and trustworthy AI.

## 12. Non-goals

This document does not claim that every cloud service is feature-equivalent.

It intentionally avoids:

- pretending OCR engines produce identical output;
- pretending vector search rankings are interchangeable;
- pretending LLMs have identical behavior;
- pretending IAM models are identical;
- hiding regional availability or data-residency constraints.

Portability is achieved through contracts and adapters, not through pretending differences do not exist.
