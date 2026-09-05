# Security, Governance, and Rollout — Cloud-Agnostic Edition

## Security boundary

Security should protect:

- source documents;
- metadata;
- credentials;
- model access;
- tools;
- outputs;
- audit records.

## Portable controls

Require:

- workload identity;
- least privilege;
- encryption at rest and in transit;
- secret management;
- network restrictions where appropriate;
- audit logging;
- retention policies;
- data classification;
- human review for defined risk states.

## Governance

Version and approve:

- prompts;
- extraction mappings;
- schemas;
- model configurations;
- retrieval configurations;
- business rules.

A change should have an owner and an observable impact.

## Human review

Human review is not a failure of agentic AI.

It is an explicit state used when:

- evidence conflicts;
- confidence is insufficient;
- a high-impact field is uncertain;
- a new source pattern has not been validated.

## Rollout

A safe rollout sequence is:

1. offline evaluation;
2. shadow execution;
3. limited production;
4. monitored expansion;
5. controlled change management.

## Portability

Security controls should be expressed as requirements first and provider implementation second.

For example:

> "The extraction worker may read documents but may not modify source objects."

The cloud-specific IAM policy is an implementation of that rule.
