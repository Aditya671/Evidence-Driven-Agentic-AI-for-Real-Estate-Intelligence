# Evaluation, Observability, and Recovery — Cloud-Agnostic Edition

## Evaluate the workflow, not only the answer

Useful evaluation dimensions include:

- source correctness;
- evidence support;
- extraction accuracy;
- normalization;
- classification;
- coverage;
- fallback frequency;
- not-found behavior;
- conflict handling.

## Golden dataset

A golden dataset should contain:

- question;
- expected entity;
- expected metric;
- expected source;
- expected value;
- acceptable variations;
- expected failure state where appropriate.

## Observability contract

Every run should emit correlated telemetry for:

```text
run
 -> request
 -> tool call
 -> retrieval
 -> evidence
 -> extraction
 -> validation
 -> enrichment
 -> output
```

Use OpenTelemetry where practical so traces, metrics, and logs can be exported to different backends.

## Recovery

Store enough state to:

- identify the failed stage;
- replay only the failed subset;
- preserve previous evidence;
- compare versions;
- audit the final decision.

## Cloud neutrality

Do not make the application depend directly on a provider's log query language.

Define application telemetry fields and export them through an observability adapter.
