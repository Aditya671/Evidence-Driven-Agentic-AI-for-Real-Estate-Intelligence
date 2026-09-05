# Foundation Before Agents — Cloud-Agnostic Edition

## Scope

This is the portable counterpart to the real-world Azure foundation document.

The central lesson remains unchanged: **agent quality is bounded by evidence quality**.

A cloud-neutral foundation consists of:

1. durable source storage,
2. deterministic document identity,
3. rich metadata,
4. reproducible parsing,
5. searchable evidence,
6. mapping-driven domain grounding,
7. versioned business rules.

## What the cloud must provide

The platform must support:

- immutable or versioned source objects;
- checksums;
- metadata;
- page-aware parsed representations;
- repeatable re-indexing;
- separation between raw and processed data;
- access controls;
- event-driven or batch ingestion.

## Portable ingestion model

```text
Source
  -> Object Store
  -> Change Detection
  -> Parser/OCR
  -> Normalization
  -> Metadata Assignment
  -> Indexing
  -> Validation
```

No agent is required to perform these steps.

## Metadata contract

At minimum capture:

- `document_id`
- `document_version`
- `asset_id`
- `document_type`
- `source_type`
- `period`
- `ingestion_timestamp`
- `checksum`
- `page_count`
- `parser_version`
- `index_version`

## Mapping layer

The mapping layer remains application-owned.

It can define:

- entity aliases,
- metric definitions,
- keywords,
- avoid terms,
- source hints,
- classifier/time context,
- extraction rules,
- conflict resolution.

Cloud services should consume these rules, not replace them.

## Portability test

If changing cloud providers forces the team to rewrite the metadata model or extraction rules, the foundation is too provider-specific.
