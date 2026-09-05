# Cloud-Agnostic Governance and Standards

## Why this file exists

A globally reusable architecture should not define governance only through a single cloud provider.

The governance layer should be expressed as:

- risk;
- control;
- evidence;
- owner;
- review cadence.

## NIST AI RMF

NIST AI RMF is a voluntary, non-sector-specific framework intended to help organizations manage AI risks and promote trustworthy AI.

For this project, its value is as a cross-cloud governance vocabulary.

## OpenTelemetry

Use OpenTelemetry for portable application telemetry where practical.

The application can then export traces, metrics, and logs to:

- CloudWatch;
- Azure Monitor;
- Google Cloud observability;
- Grafana;
- other compatible backends.

## Control examples

| Risk | Control | Evidence |
|---|---|---|
| Unsupported claim | Evidence required | citation/evidence record |
| Wrong entity | Canonical resolver | entity resolution record |
| Stale source | Version/checksum | source metadata |
| Model drift | Golden dataset | evaluation report |
| Unauthorized access | Least privilege | IAM/access review |
| Silent failure | Stage telemetry | trace/run record |
| Conflicting sources | Resolution policy | conflict record |
| Prompt regression | Versioned prompt | prompt version |
| Mapping regression | Versioned mappings | mapping version |

## ISO/organizational alignment

Organizations may additionally map these controls to their internal security, privacy, AI-management, quality, and audit frameworks.

The key design principle is that governance evidence should survive cloud migration.
