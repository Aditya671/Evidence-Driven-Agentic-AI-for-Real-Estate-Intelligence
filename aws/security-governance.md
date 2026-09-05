# AWS Security and Governance

## Identity

Use IAM roles with least privilege.

Separate roles by workload:

```text
ingestion-role
parser-role
retrieval-role
model-role
output-role
monitoring-role
```

The agent should not receive permissions unrelated to the action it is executing.

## S3 controls

Protect source buckets using:

- blocked public access;
- encryption;
- restrictive bucket policies;
- object ownership controls;
- versioning where appropriate;
- lifecycle/retention policies;
- audit logging.

## KMS

Use KMS for controlled encryption where the data classification and governance requirements justify customer-managed keys.

## Secrets

Store credentials and provider secrets in Secrets Manager.

Do not place secrets in:

- prompts;
- mappings;
- source code;
- logs;
- evidence records.

## Model access

Restrict which foundation models the workload can invoke.

Record model/provider configuration in run metadata.

## Tool authorization

Authorization must happen before tool execution.

Example:

```text
Agent requests: retrieve_document(doc-123)

Policy:
  role can READ evidence
  role cannot WRITE source

Result:
  allow read
```

## Governance

Version:

- prompts;
- mappings;
- workflow definitions;
- model configuration;
- infrastructure;
- schemas.

Every production run should be reconstructable from those versions.

## Human review

Create an explicit review state for:

- conflicting evidence;
- unsupported extraction;
- high-impact values;
- new document patterns.

## AWS-specific principle

AWS IAM is the infrastructure enforcement layer.

The application still needs its own business authorization and evidence policy.

IAM cannot decide whether a value is the correct IRR for an asset.
