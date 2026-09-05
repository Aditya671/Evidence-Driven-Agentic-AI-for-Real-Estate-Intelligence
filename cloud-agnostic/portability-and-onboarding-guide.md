# Cloud Portability and New-Cloud Onboarding Guide

## Goal

A new cloud should be onboarded by implementing capability adapters, not by rewriting the business workflow.

## Phase 1 — Contract inventory

Freeze:

- document metadata schema;
- evidence schema;
- extraction schema;
- run schema;
- telemetry schema;
- entity mappings;
- metric mappings.

## Phase 2 — Infrastructure adapters

Implement:

```text
ObjectStore
DocumentParser
SearchIndex
ModelProvider
WorkflowRuntime
SecretProvider
TelemetryExporter
```

## Phase 3 — Golden corpus

Run the same representative documents through:

- ingestion;
- parsing;
- indexing;
- retrieval;
- extraction.

Compare evidence quality rather than only final answers.

## Phase 4 — Retrieval parity

Measure:

- source recall;
- document selection accuracy;
- chunk relevance;
- table/page escalation success;
- false retrievals.

Do not require identical search rankings.

Require equivalent business evidence.

## Phase 5 — Model parity

Compare:

- extraction accuracy;
- unsupported-claim rate;
- schema validity;
- failure-state accuracy.

A different model is acceptable if it satisfies the workflow contract.

## Phase 6 — Operational parity

Verify:

- retry behavior;
- timeouts;
- run tracking;
- audit logging;
- telemetry;
- alerting;
- replay.

## Phase 7 — Security parity

Verify:

- workload identity;
- least privilege;
- encryption;
- secrets;
- network boundaries;
- retention;
- data residency.

## Phase 8 — Cutover

Prefer:

1. shadow mode;
2. parallel runs;
3. evidence comparison;
4. controlled traffic;
5. rollback path.

## Definition of done

A cloud onboarding is complete when the same business question produces:

- equivalent evidence;
- equivalent provenance;
- equivalent validation behavior;
- equivalent explicit failure states;
- equivalent auditability.

It does not need to use identical infrastructure.
