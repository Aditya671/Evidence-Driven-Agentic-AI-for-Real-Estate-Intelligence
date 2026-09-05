# 06. Azure-Centered Current-State Architecture

## Purpose

This document describes the current-state architecture used as the learner series reference model. The system combines an Azure document intelligence platform with a governed metric extraction runtime.

## Architecture Layers

The reference architecture has two complementary layers.

First, the application and retrieval platform in `the application and retrieval platform` provides the Azure-centered foundation:

- Chainlit application experience.
- Azure OpenAI reasoning and response generation.
- Azure AI Search retrieval over indexed enterprise documents.
- LlamaIndex-based retrieval and workflow components.
- Cosmos DB persistence for chat threads and user session state.
- User-upload indexing with thread-scoped storage and retrieval.
- Bulk indexing pipelines for curated document sets.

Second, the extraction runtime in `the metric extraction runtime` provides repeatable, metric-specific extraction:

- the metric extraction runtime for the extraction agent.
- Mapping-driven query generation.
- Tool-sequenced retrieval and evidence gathering.
- Lean JSON extraction.
- Post-extraction enrichment.
- CSV exports, monitoring reports, archive folders, and run registry outputs.

## Logical Flow

The production pattern is:

1. Store raw documents in governed enterprise storage.
2. Index documents into Azure AI Search with chunk text, embeddings, file metadata, document identifiers, and freshness attributes.
3. Use alias and entity grounding to normalize user or batch inputs.
4. Narrow candidate source documents using file-path and metadata search.
5. Retrieve relevant chunks from selected documents before falling back to broad semantic search.
6. Extract a lean JSON answer from evidence.
7. Enrich the result with scale, normalized value, reporting basis, time basis, output level, table flags, and value resolution method.
8. Publish CSVs and monitoring artifacts.

## Why This Is Azure-Centered

The series should be read as an Azure architecture guide, not as a production architecture guide. The target operating environment is built around Azure services for search, storage, application hosting, identity, observability, and model access.


## Current Tooling Model

The extraction agent is intentionally modular:

- Alias normalization prevents noisy entity names from corrupting retrieval.
- Path search creates a small candidate set of likely documents.
- Chunk filtering retrieves evidence inside selected documents.
- Semantic fallback is controlled and auditable.
- Table tooling supports financial reports where the answer often lives in rows, columns, and subtotals rather than prose.

This separation makes the system easier to debug. If IRR is wrong, the team can inspect whether the alias was wrong, the file was wrong, the chunk was wrong, the table interpretation was wrong, or the normalization step was wrong.



