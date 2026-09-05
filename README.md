# Evidence-Driven Agentic AI for Real-Estate Intelligence

This repository now has **two intentionally separate architecture tracks**:

1. **Real-world Azure implementation** — preserved from the actual work and kept intact.
2. **Cloud-neutral + AWS implementation** — generalized so the architecture can be onboarded to another cloud without pretending the real implementation was multi-cloud.

## Documentation model

```text
                    Evidence-Driven Architecture
                              |
             +----------------+----------------+
             |                                 |
             v                                 v
     REAL-WORLD IMPLEMENTATION          GENERALIZED DESIGN
             |                                 |
             v                        +--------+--------+
     real-world/azure/               |                 |
                                     v                 v
                              cloud-agnostic        aws/
```

### Real-world Azure track

`real-world/azure/` contains the Azure-based architecture, core documentation, production blueprint, evidence observations, readiness checklist, and validation report.

These files are preserved as implementation evidence.

### Cloud-neutral track

`architecture/`, `core/cloud-agnostic/`, and `cloud-agnostic/` describe the generalized architecture and portability contracts.

### AWS track

`aws/` maps those contracts to current AWS capabilities including S3, Textract, OpenSearch, Bedrock, Bedrock AgentCore, Step Functions, IAM, KMS, and CloudWatch.

## Why the separation matters

The project should not claim:

> "This architecture is multi-cloud because we replaced Azure service names."

Instead:

> **The real Azure implementation is evidence. The cloud-neutral architecture extracts the reusable engineering principles from that evidence. AWS is one concrete implementation of those principles.**

This preserves technical credibility while making the work reusable.

## Core architectural principle

The system is built around:

**Business Question → Entity + Metric Grounding → Source Discovery → Scoped Evidence Retrieval → Page/Table Verification → Lean JSON Extraction → Validation + Conflict Resolution → Enrichment → Evidence-Bearing Output → Evaluation + Observability + Recovery**

The central idea is simple:

> **Retrieval trust comes before agent cleverness.**

## Important separation

Cloud providers supply infrastructure capabilities.

The application owns:

- entity resolution;
- metric mappings;
- extraction rules;
- evidence contracts;
- workflow semantics;
- validation;
- provenance;
- evaluation;
- governance.

## Tracks

### Real-world Azure

- `real-world/azure/architecture/`
- `real-world/azure/core/`
- `real-world/azure/evidence/`
- `real-world/azure/production/`
- `real-world/azure/validation/`

### Cloud-neutral

- `architecture/cloud-agnostic-reference-architecture.md`
- `architecture/cloud-capability-model.md`
- `core/cloud-agnostic/`
- `cloud-agnostic/file-by-file-analysis-and-crosswalk.md`
- `cloud-agnostic/portability-and-onboarding-guide.md`
- `cloud-agnostic/governance-and-standards.md`

### AWS

- `aws/reference-architecture.md`
- `aws/service-mapping.md`
- `aws/implementation-blueprint.md`
- `aws/security-governance.md`
- `aws/observability-and-evaluation.md`

## Portability rule

A new cloud should require changes primarily to infrastructure adapters, not to:

- business rules;
- evidence schemas;
- mappings;
- evaluation datasets;
- workflow semantics;
- provenance;
- governance controls.

That is the standard this documentation now targets.
