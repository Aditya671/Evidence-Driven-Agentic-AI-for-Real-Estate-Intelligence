# Maintainable Agentic Workflow — Cloud-Agnostic Edition

## Architecture principle

Separate:

- orchestration,
- capabilities,
- infrastructure,
- business rules,
- output processing.

An agent decides **what capability is needed**.

A tool executes **how that capability is performed**.

## Portable workflow

```text
Request
  -> Ground
  -> Discover
  -> Retrieve
  -> Verify
  -> Extract
  -> Validate
  -> Resolve
  -> Enrich
  -> Publish
```

## Capability interfaces

Useful application-level interfaces include:

```text
SourceDiscovery
DocumentRetrieval
SemanticFallback
TableVerification
MetricExtraction
Validation
Enrichment
Export
```

## Runtime choices

The workflow can run in:

- managed workflow services;
- container orchestration;
- serverless functions;
- durable workflow engines;
- application-managed async workers.

The workflow semantics should stay constant.

## Retry design

Retry infrastructure failures.

Do not blindly retry semantic failures.

For example:

- timeout → retry;
- transient provider error → retry;
- malformed model output → repair/retry under a bounded policy;
- metric not found → do not retry indefinitely;
- conflicting sources → escalate or resolve according to rule.

## Packaging

Version:

- code;
- prompts;
- mappings;
- schemas;
- model configuration;
- workflow definition.

A run should record all versions used.
