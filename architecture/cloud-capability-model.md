# Cloud Capability Model

## Purpose

This file defines the capabilities the evidence-driven real-estate intelligence system requires before selecting a cloud provider.

The capability is the architectural requirement. The service is an implementation choice.

## Capability matrix

| Domain | Required capability | Minimum acceptance criteria |
|---|---|---|
| Ingestion | Object/document intake | checksum, version, metadata, idempotency |
| Parsing | Text extraction | page-aware output |
| Layout | Table/form/layout extraction | structural relationships retained |
| Catalog | Source metadata | asset, document type, period, source, version |
| Search | Exact/metadata search | deterministic filters |
| Search | Semantic search | relevance ranking and metadata filters |
| Evidence | Page/table addressing | stable locator |
| Extraction | Structured output | schema validation |
| Enrichment | Post-processing | separated from extraction |
| Orchestration | Durable workflow | retries, timeouts, replay |
| Agent | Tool selection | explicit tool contract |
| State | Run registry | lifecycle/status |
| Observability | Traceability | end-to-end correlation |
| Security | Identity | least privilege |
| Governance | Change control | versioned prompts/rules |
| Evaluation | Golden dataset | stage-level metrics |
| Human review | Review queue | evidence-bearing review package |

## Provider selection questions

For every candidate platform ask:

1. Can the document pipeline preserve page and table context?
2. Can metadata be filtered deterministically?
3. Can search be separated from generation?
4. Can a workflow retry a failed stage without repeating the entire run?
5. Can model providers be changed?
6. Can telemetry leave the provider?
7. Can the system expose source-level provenance?
8. Can access be constrained by workload identity?
9. Can data residency be enforced?
10. Can the system be operated without depending on a proprietary agent abstraction?

## Adapter boundaries

Create adapters around:

- `ObjectStore`
- `DocumentParser`
- `SearchIndex`
- `EmbeddingProvider`
- `ModelProvider`
- `WorkflowRuntime`
- `SecretProvider`
- `TelemetryExporter`

Do **not** create adapters around business concepts such as `IRRExtractor`.

Business logic should remain above infrastructure adapters.

## Portability test

A platform implementation is portable when a provider replacement requires changes primarily in adapters and infrastructure configuration rather than:

- extraction rules,
- evidence schemas,
- evaluation datasets,
- workflow semantics,
- business outputs,
- governance controls.

That is the practical definition of portability for this project.
