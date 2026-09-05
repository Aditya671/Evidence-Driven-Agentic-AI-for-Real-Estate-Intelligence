# AWS Implementation Blueprint

## Phase 1 — Foundation

Create:

- S3 buckets/prefixes;
- IAM roles;
- KMS keys;
- PostgreSQL state store;
- application configuration;
- document metadata contract.

## Phase 2 — Indexing

Implement:

```text
S3 object
 -> checksum
 -> metadata
 -> Textract
 -> normalized representation
 -> OpenSearch indexing
```

Persist parser/version metadata.

Make ingestion idempotent.

## Phase 3 — Grounding and retrieval

Implement:

```text
asset resolver
metric resolver
time resolver
source discovery
document-scoped retrieval
semantic fallback
table/page escalation
```

Do not let a single vector query replace this sequence.

## Phase 4 — Agent/workflow

Use a containerized application or AgentCore runtime for agent execution.

Use Step Functions for durable business workflow orchestration when needed.

Example:

```text
Start
  -> Ground Request
  -> Discover Sources
  -> Retrieve Evidence
  -> Verify Table/Page
  -> Extract
  -> Validate
  -> Resolve Conflict
  -> Enrich
  -> Publish
  -> Record Run
```

## Phase 5 — Output

Write:

- evidence-bearing JSON;
- business CSV;
- database records;
- review packages.

Never discard raw evidence after enrichment.

## Phase 6 — Evaluation

Run a golden corpus.

Measure:

- source accuracy;
- evidence support;
- extraction accuracy;
- invalid output rate;
- fallback rate;
- review rate;
- not-found correctness.

## Phase 7 — Production

Add:

- alarms;
- dashboards;
- retry policies;
- dead-letter handling;
- replay tools;
- retention policies;
- access reviews;
- change approvals.

## Phase 8 — Migration readiness

Keep provider-specific code inside adapters.

A future Azure/GCP/private-cloud implementation should reuse:

- mappings;
- schemas;
- golden datasets;
- business workflow;
- evaluation logic.
