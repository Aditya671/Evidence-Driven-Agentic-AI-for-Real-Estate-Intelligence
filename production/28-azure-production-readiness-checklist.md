# 28. Azure Production Readiness Checklist

## Foundation

* Confirm business use case and expected users.
* Confirm document families and source systems.
* Confirm initial metrics, such as IRR and rent growth rate.
* Confirm data sensitivity and publication boundaries.
* Confirm Azure subscription, resource groups, identity model, and access controls.

## Indexing

* Confirm raw documents are stored in governed storage.
* Confirm path metadata is complete.
* Confirm chunk metadata includes document identifier, page, file path, timestamps, checksum, and source fields.
* Confirm embeddings are generated consistently.
* Confirm Azure AI Search indexes are queryable by path, metadata, and semantic search.

## Retrieval

* Confirm alias normalization works for known entity variations.
* Confirm file-path discovery returns expected source documents.
* Confirm doc-id-scoped chunk retrieval works.
* Confirm broad semantic fallback is controlled and observable.
* Confirm table tooling is used for structured reports.

## Extraction

* Confirm mapping rules exist for each metric and classifier.
* Confirm query generation uses description, source hints, keywords, avoid terms, and extraction rules.
* Confirm raw JSON schema validation.
* Confirm not-found and not-applicable behavior.
* Confirm invalid outputs are archived.

## Enrichment

* Confirm normalized value and display value behavior.
* Confirm scale and unit handling.
* Confirm reporting basis and time basis classification.
* Confirm table evidence flags.
* Confirm value resolution method.

## Observability

* Confirm event logs are written.
* Confirm monitoring state is updated.
* Confirm classifier coverage reports are generated.
* Confirm run registry is populated.
* Confirm status reports and upload manifests are available where applicable.

## Security and Compliance

* Confirm secrets are not hard-coded.
* Confirm environment variables and secure configuration are used.
* Confirm least-privilege access.
* Confirm audit trail requirements.
* Confirm retention policy for raw, processed, archive, and monitoring artifacts.

## Human Readiness

* Confirm reviewer workflow.
* Confirm escalation path for bad values.
* Confirm runbook coverage.
* Confirm training material.
* Confirm ownership and RACI.
