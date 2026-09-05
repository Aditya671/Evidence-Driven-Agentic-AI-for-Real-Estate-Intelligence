# AWS Observability and Evaluation

## Observability architecture

```text
Agent / API / Worker
       |
       v
OpenTelemetry instrumentation
       |
       +--> traces
       +--> metrics
       +--> logs
       |
       v
CloudWatch / external backend
```

## Correlation

Every event should carry:

- `run_id`;
- `request_id`;
- `session_id`;
- `document_id`;
- `evidence_id`;
- `tool_call_id`.

## Retrieval telemetry

Record:

- retrieval strategy;
- query;
- filters;
- candidate count;
- selected evidence;
- fallback reason;
- latency.

## Extraction telemetry

Record:

- metric;
- extraction rule version;
- model configuration;
- schema result;
- validation result;
- conflict status.

Do not log sensitive source content unnecessarily.

## Workflow metrics

Track:

- success rate;
- stage failure rate;
- retry rate;
- not-found rate;
- review rate;
- latency;
- cost;
- throughput.

## Evaluation

CloudWatch tells you whether the system is running.

The golden dataset tells you whether the system is correct.

These are different concerns.

## AgentCore

AgentCore provides service-level agent observability and standardized telemetry. Use it where available, but retain application-level instrumentation so the evidence workflow remains portable.

## Failure replay

Persist enough run state to replay:

```text
failed retrieval
failed extraction
failed validation
```

without reprocessing unrelated successful stages.

## Operational rule

Never define production quality as:

> "The API returned HTTP 200."

A successful run must also satisfy evidence and business validation criteria.
