# 23. Code-Derived Indexing and Extraction Observations

## Purpose

This document records implementation observations from the reference implementation areas. It connects the learner series architecture to the codebase so the series stays grounded in the working system.

## Reference Implementation Areas

* `the application and retrieval platform`: Azure-centered application, chat, indexing, and retrieval platform.
* `the metric extraction runtime`: metric extraction runtime and supporting batch workflows.

## Application Platform Observations

The application and retrieval platform provides the broader document intelligence foundation:

* Chainlit is the primary interactive user experience.
* Azure AI Search provides enterprise retrieval over indexed documents.
* Azure OpenAI provides reasoning and response generation.
* Cosmos DB persists chat threads and session context.
* User uploads are indexed into thread-scoped locations so uploaded files can be retrieved within the correct conversation context.
* Bulk indexing modules process curated document sets into searchable chunks and embeddings.

## Extraction Runtime Observations

The metric extraction runtime is implemented as a reusable module rather than a one-off script. It has moved beyond an exploratory prototype into a module-based runtime with repeatable extraction behavior.

Notable patterns:

* Prompt artifacts are package-local and loaded by the runtime.
* Query generation is prompt-based and aligned to the mapping layer.
* The tool sequence starts with alias normalization and source narrowing before chunk retrieval.
* The agent writes lean JSON for raw extraction.
* Enrichment agents add reporting basis, time basis, value subtype, scale, normalized value, display value, table evidence flags, output level, and resolution method.
* Monitoring files track completed, skipped, failed, retried, and fallback behavior.

## Retrieval Observations

The retrieval design is intentionally layered:

1. Alias normalization reduces entity ambiguity.
2. File-path search identifies likely source documents.
3. Doc-id-scoped chunk filtering retrieves evidence from selected documents.
4. Semantic fallback is available but controlled.
5. Table tooling handles structured financial evidence.

This hierarchy is important because a production system should not let broad vector search decide everything.

## Output Observations

The extraction runtime separates:

* Raw extraction JSON.
* Enriched export records.
* Archive records.
* Monitoring state.
* Event logs.
* Run registry data.

This separation supports auditability and recovery. If an extracted rent growth rate is wrong, engineers can inspect whether the issue came from retrieval, table reading, extraction, normalization, or export.

## Learner Series Implication

The series should teach the system as a complete production pipeline:

* Indexing and metadata first.
* Mapping and extraction rules second.
* Tool-orchestrated retrieval third.
* Lean extraction fourth.
* Enrichment, validation, and monitoring fifth.
* Human review and operational readiness throughout.
