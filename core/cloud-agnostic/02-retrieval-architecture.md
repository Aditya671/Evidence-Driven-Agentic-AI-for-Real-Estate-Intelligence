# Retrieval Architecture — Cloud-Agnostic Edition

## Core principle

Retrieval is a controlled evidence acquisition process, not simply "RAG."

The portable pattern is:

**source discovery → document selection → scoped evidence retrieval → semantic fallback → table/page verification**

## Stage 1: source discovery

Use deterministic metadata filters first:

- asset;
- source type;
- reporting period;
- document type;
- path/category;
- document version.

This reduces the search space before semantic retrieval.

## Stage 2: document-scoped retrieval

Once a document is selected, retrieve within that document whenever possible.

The portable interface is:

```text
retrieve_chunks(
    document_id,
    query,
    metadata_filters
)
```

The implementation can be OpenSearch, Elasticsearch, Azure AI Search, PostgreSQL, or another search engine.

## Stage 3: controlled semantic fallback

Semantic search is a fallback, not a license to search the entire corpus.

Record:

- why fallback was invoked;
- query;
- candidate count;
- selected evidence;
- confidence/relevance metadata.

## Stage 4: table/page escalation

Financial documents frequently contain information whose meaning depends on layout.

When flattened text is insufficient:

```text
chunk evidence
    ↓ insufficient
page evidence
    ↓ insufficient
table evidence
    ↓
extract + validate
```

## Retrieval contract

Every retrieval response should include:

```json
{
  "evidence_id": "e-001",
  "document_id": "doc-001",
  "page": 42,
  "locator": "table:returns",
  "text": "...",
  "retrieval_strategy": "document_scoped",
  "query_id": "q-001"
}
```

## Portability rule

The workflow must never know whether the underlying search engine uses BM25, HNSW, hybrid retrieval, proprietary ranking, or another mechanism.

It only consumes the retrieval contract.
