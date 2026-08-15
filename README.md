# Evidence-Driven-Agentic-AI-for-Real-Estate-Intelligence

# Azure-Centric Agentic AI Learner Series

This is a public-facing learning path for building a production-ready agentic AI system over unstructured and semi-structured enterprise documents. It is intentionally broader than a single blog post: the series starts with foundations and prerequisites, then moves through architecture, indexing, retrieval, agent workflow design, metadata governance, validation, observability, security, compliance, and operational handover.

The reference architecture uses two generic implementation areas:

- Application and retrieval platform: the Azure-centered application layer for document indexing, chat workflows, user uploads, Azure AI Search, Azure OpenAI, Chainlit-style user experiences, and conversation persistence.
- Metric extraction runtime: the governed extraction layer for mapping-driven query generation, tool-sequenced retrieval, lean JSON outputs, enrichment, monitoring, and export-ready CSVs.

## What This Series Is For

Use this series when you want to understand how to build the full system, not just the prompt. A production agentic AI solution needs document ingestion, metadata, search indexes, extraction rules, tool policies, validation, data contracts, monitoring, recovery behavior, and a human operating model.

The examples use real estate intelligence concepts such as IRR and rent growth rate because they make the problem concrete. The same approach can be applied to many other variables, metrics, facts, clauses, risks, or operational attributes.

## Current System Reference Model

The series uses an Azure-centered reference architecture:

- Azure Storage or equivalent enterprise document storage for raw files and generated artifacts.
- Azure AI Search for file-path discovery, chunk retrieval, semantic search, and metadata-filtered evidence lookup.
- Azure OpenAI for reasoning, query rewriting, extraction, enrichment, and validation assistance.
- Application services for interactive workflows, user uploads, chat persistence, retrieval orchestration, and analyst review.
- A metric extraction runtime for repeatable extraction runs, tool sequencing, lean JSON outputs, enrichment agents, monitoring, and export-ready CSVs.

## Reading Order

Read the numbered files in order. The series is structured as a production build path:

- `00` starts with the indexing foundation.
- `01` through `05` define the use case, requirements, plan, and RACI.
- `06` through `11` explain architecture, workflow design, data flow, indexing, freshness, and incremental reindexing.
- `12` through `17` define data contracts, quality, observability, recovery, security, compliance, and auditability.
- `18` onward covers production architecture, service mapping, implementation, operational handover, validation, readiness, and code-alignment guidance.

## Public Sanitization Policy

These documents are written as learning material. Do not publish tenant names, client names, raw storage paths, subscription identifiers, secrets, index names that reveal internal context, or proprietary document examples. When sharing externally, describe implementation patterns and use generic examples such as IRR, rent growth rate, investment memo, source document, selected record, and supporting evidence.

## Core Design Principles

1. Treat retrieval as an engineering system, not a prompt trick.
2. Keep raw documents, search indexes, extracted values, and exported data connected through provenance.
3. Write extraction rules per variable: where to look first, how to extract, how to validate or calculate, and how to resolve conflicts.
4. Prefer narrow, metadata-scoped retrieval before broad semantic search.
5. Separate raw extraction from enrichment, normalization, quality checks, and reporting.
6. Make failures visible through monitoring files, event logs, retry metadata, and archive folders.
7. Keep humans in control through clear data contracts, repeatable runs, and explainable evidence.
