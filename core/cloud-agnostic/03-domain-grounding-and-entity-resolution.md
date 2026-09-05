# Domain Grounding and Entity Resolution — Cloud-Agnostic Edition

## Why grounding matters

A user may ask for:

> "What was the IRR for the property in 2025?"

That sentence hides several decisions:

- Which property?
- Which entity alias?
- Which reporting period?
- Which IRR definition?
- Which source?
- Which document version?

The grounding layer resolves those decisions before extraction.

## Portable grounding model

```text
User Request
  -> Entity Resolver
  -> Metric Resolver
  -> Time Resolver
  -> Source Hints
  -> Retrieval Plan
```

## Entity hierarchy

Support relationships such as:

```text
Portfolio
  └── Asset
       └── Building
            └── Unit / Component
```

The exact hierarchy is domain-specific; the infrastructure is not.

## Alias model

Aliases should be stored as governed data:

```json
{
  "canonical_id": "asset-123",
  "canonical_name": "Example Property",
  "aliases": ["Example Tower", "EPT", "Property 123"]
}
```

## Metric mapping

A mapping can contain:

- canonical metric;
- definition;
- keywords;
- avoid terms;
- expected type;
- classifier;
- source hints;
- extraction rule;
- conflict strategy.

## Cloud-neutral requirement

Do not encode entity resolution inside a cloud search index alone.

The search engine can improve candidate retrieval, but canonical identity must remain application-owned and testable.
