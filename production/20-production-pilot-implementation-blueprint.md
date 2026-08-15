# 20. Production Pilot Implementation Blueprint

## Purpose

This blueprint describes how to implement a proof of concept that can grow into production. The goal is to prove the full lifecycle: ingest documents, index them, retrieve evidence, extract values, validate outputs, and monitor operations.

## Phase 1: Foundation

Establish the Azure-centered foundation:

* Confirm document sources and storage layout.
* Define document metadata and freshness fields.
* Configure Azure AI Search indexes for path and chunk retrieval.
* Configure Azure OpenAI access through environment variables and secure configuration.
* Define the initial metrics, such as IRR and rent growth rate.

## Phase 2: Indexing

Build the indexing path:

* Process raw documents into text and chunks.
* Preserve file path, document identifier, page, checksum, modified date, upload date, and source metadata.
* Generate embeddings.
* Publish chunks and metadata to Azure AI Search.
* Validate that file-path search and chunk retrieval return expected evidence.

## Phase 3: Mapping and Query Generation

Create metric definitions:

* Business definition.
* Keywords and aliases.
* Avoid terms.
* Preferred sources.
* Classifier behavior.
* Extraction rules using the pattern: where to look, how to extract, how to validate, and how to resolve conflicts.

The query generator should convert these definitions into retrieval intent before the extraction agent uses tools.

## Phase 4: Agent Workflow

Implement the controlled workflow:

1. Normalize entity aliases.
2. Search candidate file paths.
3. Retrieve doc-scoped chunks.
4. Use semantic fallback only when needed.
5. Use table tooling for structured values.
6. Extract lean JSON.
7. Validate schema.
8. Archive invalid responses.

## Phase 5: Enrichment and Export

Add post-extraction enrichment:

* Scale and normalized value.
* Display value and display unit.
* Reporting basis.
* Time basis.
* Value subtype.
* Table evidence flag.
* Output level.
* Resolution method.

Export the results to CSV and maintain monitoring state.

## Phase 6: Evaluation

Build a small but representative test set:

* Direct IRR values.
* Table-based IRR values.
* Stated rent growth rates.
* Rent growth values requiring calculation or validation.
* Missing values.
* Conflicting values.
* Ambiguous asset names.

Score source selection, citation support, extraction accuracy, normalization, and not-found behavior.

## Phase 7: Production Readiness

Before production rollout:

* Review security and RBAC.
* Confirm no secrets are hard-coded.
* Confirm logs and artifacts are retained according to policy.
* Confirm recovery procedures.
* Confirm monitoring reports.
* Confirm analyst review workflow.
* Confirm data contracts with downstream consumers.
