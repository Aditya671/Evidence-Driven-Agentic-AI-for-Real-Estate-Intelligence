# File-by-File Cloud Portability Analysis and Crosswalk

## Purpose

This is the detailed analysis of the existing real-world Azure documentation and how each file maps into the cloud-neutral architecture.

The Azure track is preserved as-is under:

```text
real-world/azure/
```

The new cloud-neutral track is intentionally separate.

## 1. Current-State Azure Architecture

**Original:** `real-world/azure/architecture/current-state-azure-architecture.md`

### What it establishes

This file describes the actual Azure-centered implementation: document intelligence, search, model inference, workflow orchestration, monitoring, and operational boundaries.

### What is portable

- layered architecture;
- separation of application and infrastructure;
- retrieval as a distinct capability;
- extraction versus enrichment;
- monitoring and traceability.

### What is Azure-specific

- Azure AI Search;
- Azure OpenAI;
- Azure storage/services;
- Azure-oriented deployment assumptions.

### Portable replacement

`architecture/cloud-agnostic-reference-architecture.md`

### AWS implementation

`aws/reference-architecture.md`

**Decision:** keep the Azure file untouched because it is evidence of real implementation, not the universal reference architecture.

## 2. Foundation Before Agents

**Original:** `real-world/azure/core/01-foundation-before-agents.md`

### Portable concepts

- indexing before agents;
- metadata;
- versioning;
- source organization;
- mappings;
- raw/processed separation.

### Cloud-specific concepts

The implementation details of storage, parsing, indexing, and embedding.

### Portable treatment

`core/cloud-agnostic/01-foundation-before-agents.md`

## 3. Retrieval Architecture

**Original:** `real-world/azure/core/02-retrieval-architecture.md`

### Portable concepts

- source discovery;
- document-scoped retrieval;
- semantic fallback;
- table/page escalation;
- evidence metadata.

### Provider-specific implementation

The search service, ranking algorithms, vector engine, and query syntax.

### Portable treatment

`core/cloud-agnostic/02-retrieval-architecture.md`

## 4. Domain Grounding and Entity Resolution

**Original:** `real-world/azure/core/03-domain-grounding-and-entity-resolution.md`

### Portable concepts

- asset hierarchy;
- aliases;
- metric mappings;
- classifier/time context;
- extraction rules;
- conflict resolution.

### Key architecture decision

These belong to the application/domain layer, not to the cloud search service.

### Portable treatment

`core/cloud-agnostic/03-domain-grounding-and-entity-resolution.md`

## 5. System Prompts as Operating Policy

**Original:** `real-world/azure/core/04-system-prompts-as-operating-policy.md`

### Portable concepts

- prompts as policy;
- tool constraints;
- evidence requirements;
- schema adherence;
- mapping/prompt separation.

### Cloud-specific risk

Provider-specific model APIs and structured-output mechanisms.

### Portable treatment

`core/cloud-agnostic/04-system-prompts-as-operating-policy.md`

## 6. Maintainable Agentic Workflow

**Original:** `real-world/azure/core/05-maintainable-agentic-workflow.md`

### Portable concepts

- capability/tool separation;
- orchestration;
- retries;
- packaging;
- versioning;
- tests.

### Provider-specific implementation

Function hosting, workflow engine, agent runtime, container runtime.

### Portable treatment

`core/cloud-agnostic/05-maintainable-agentic-workflow.md`

## 7. Data Contracts and Business Outputs

**Original:** `real-world/azure/core/06-data-contracts-and-business-outputs.md`

### Portable concepts

Almost the entire file:

- JSON-first extraction;
- enrichment separation;
- explicit states;
- business-ready output;
- provenance.

### Portable treatment

`core/cloud-agnostic/06-data-contracts-and-business-outputs.md`

## 8. Evaluation, Observability, and Recovery

**Original:** `real-world/azure/core/07-evaluation-observability-and-recovery.md`

### Portable concepts

- stage-aware evaluation;
- golden datasets;
- run registry;
- retry/fallback;
- replayable failure subsets.

### Provider-specific implementation

Logging, tracing, metrics, alerting backends.

### Portable treatment

`core/cloud-agnostic/07-evaluation-observability-and-recovery.md`

### Strong recommendation

Use OpenTelemetry at the application boundary so telemetry can move between cloud backends.

## 9. Security, Governance, and Rollout

**Original:** `real-world/azure/core/08-security-governance-and-rollout.md`

### Portable concepts

- least privilege;
- secure configuration;
- auditability;
- change control;
- human review;
- staged rollout.

### Provider-specific implementation

IAM, managed identity, key management, network controls, policy services.

### Portable treatment

`core/cloud-agnostic/08-security-governance-and-rollout.md`

## 10. Technical Blog

**Original:** `real-world/azure/core/automating-real-estate-intelligence-with-agentic-ai-workflows.md`

This is a public-facing narrative derived from the real-world implementation.

It should remain in the Azure evidence track rather than being silently rewritten as if the implementation had always been cloud-neutral.

A future public article can reference the cloud-neutral architecture as a generalized design derived from the implementation.

## 11. Code-Derived Observations

**Original:** `real-world/azure/evidence/code-derived-indexing-and-extraction-observations.md`

This is evidence about the real system.

It should remain Azure/real-world because its value is precisely that it is code-derived rather than hypothetical.

## 12. Production Pilot Blueprint

**Original:** `real-world/azure/production/production-pilot-implementation-blueprint.md`

This is implementation evidence.

The portable equivalent should describe phases without claiming identical service deployment.

AWS-specific implementation belongs in:

`aws/implementation-blueprint.md`

## 13. Azure Production Readiness Checklist

**Original:** `real-world/azure/production/azure-production-readiness-checklist.md`

This should remain Azure-specific.

A provider-neutral checklist should evaluate capabilities first and provider implementations second.

The new capability model provides that boundary.

## 14. Architecture Validation Report

**Original:** `real-world/azure/validation/architecture-validation-report.md`

This is historical/implementation validation evidence.

Do not rewrite it as a generic claim.

Instead:

- preserve it;
- use it as evidence;
- derive generalized architecture principles separately.

## Overall conclusion

The correct structure is **not**:

```text
Azure docs -> rewrite everything -> pretend multi-cloud
```

It is:

```text
REAL-WORLD EVIDENCE
       |
       v
Azure implementation track
       |
       v
Generalized architecture principles
       |
       +-------------------+
       |                   |
       v                   v
Cloud-neutral track     AWS track
```

This preserves credibility while making the architecture reusable.
