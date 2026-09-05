# Data Contracts and Business Outputs — Cloud-Agnostic Edition

## Raw versus enriched

Keep extraction separate from enrichment.

### Raw extraction

Answers:

> What does the source say?

### Enrichment

Answers:

> What can the business system calculate or normalize from that value?

This separation makes provider migration safer.

## Example

```json
{
  "metric": "irr",
  "value": 18.4,
  "unit": "percent",
  "status": "valid",
  "source": {
    "document_id": "doc-001",
    "page": 42
  }
}
```

An enrichment layer can add:

```json
{
  "normalized_value": 0.184,
  "display_value": "18.4%"
}
```

## Explicit states

Use:

- `not_found`
- `not_applicable`
- `invalid`
- `pending`
- `conflict`
- `review_required`

This prevents missing evidence from being mistaken for zero, null, or success.

## Business-ready outputs

Outputs can be:

- JSON;
- CSV;
- relational records;
- APIs;
- review packages;
- downstream analytics datasets.

The output schema should be cloud-neutral.

## Provenance

Every business value must remain traceable to evidence.

A cloud provider can store provenance, but the provenance model belongs to the application.
