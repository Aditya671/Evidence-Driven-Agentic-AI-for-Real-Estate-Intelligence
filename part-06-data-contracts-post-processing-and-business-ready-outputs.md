# Part 6: Your Agent Is Only As Good As Its Output Contract

A production agentic AI system does not stop when the model produces an answer.

That answer still has to survive parsing, validation, aggregation, QA, and downstream business use.

This is where many promising AI systems quietly collapse. The agent appears intelligent in an interactive session, but its outputs are inconsistent, hard to validate, and painful to integrate.

The metric extraction runtime avoids that trap by treating output shape as a first-class architecture concern.

## The real product is not the raw model response

In a production extraction system, the business usually does not consume the raw LLM output directly.

They consume:

* a parsed row
* a normalized dataset
* a CSV export
* a QA-ready artifact
* a traceable business record

That is why the output contract matters so much. If the system cannot shape its answers consistently, it cannot become part of a dependable workflow.

## What this design does well

The workflow prompt, parser, and runner all point toward the same contract.

The final row shape includes things like:

* entity context
* value context
* evidence context
* metadata context
* comments and traceability

The data-contract design reinforces the same idea from the architecture side.

This is a strong signal that output design was treated as system design, not as cleanup after generation.

One especially useful detail from the workflow design is that the output contract does not only capture the answer. It also captures the retrieval trail. Fields such as `rewritten_query`, `file_path`, `citations`, `doc_id`, `has_table`, and `output_level` help explain how the row was produced and how strong the evidence path was.

## Why the raw-to-enriched JSON path is useful

The current extractor writes a lean accepted-value JSON object under a `results` payload, then normalizes and enriches that record before final export. The parser still accepts older markdown-table responses for backward compatibility, but the active runtime is JSON-first.

At first glance, that may look unusual. In practice, it is very practical.

It creates a bridge between:

* model-friendly structured output
* human-inspectable raw output
* parser-friendly normalized output

That means the team can inspect raw responses, debug parsing issues, and reprocess outputs without rerunning the whole workflow.

It also means the prompt, parser, and export layers are all speaking the same language. The model is not free to invent a fresh structure each time. It has to emit into a known envelope that downstream code can validate and recover.

## The layered output pattern is very human-friendly

The batch runner keeps multiple artifact layers:

* raw accepted-value responses
* active `json_format` outputs and retry archives
* processed CSV outputs
* unsuccessful rows
* failed-query artifacts
* extraction monitoring, status reports, and master-data exports

This is an excellent operational design because it matches how real teams investigate issues.

When something looks wrong, a human can ask:

* what did the model actually say?
* how did the parser interpret it?
* why did a row land in the unsuccessful output?
* which query failed?

That is much easier than trying to debug from one flattened final export.

The parser and formatter design is also more resilient than it may first appear. In `the extraction runtime's parser and formatter layer`, the system preserves compatibility with older markdown-style responses while normalizing the current JSON-first outputs into a richer export schema. This lets the workflow evolve without abandoning historical artifacts.

## Success and failure are both modeled explicitly

A mature system does not just celebrate success rows. It also makes non-success visible.

In this system, that means:

* failed queries are stored
* parse failures are summarized
* successful and unsuccessful outputs are separated
* reruns can focus on unresolved cases

That is exactly how an AI workflow becomes maintainable.

## Why the business-ready CSV matters so much

One of the most practical choices in this design is that the final target is a user-ready export written into a governed processed-output layer.

That may sound old-fashioned, but it is actually very smart.

CSV is often the format that bridges:

* analysts
* QA reviewers
* downstream scripts
* spreadsheets
* reporting workflows

A polished AI system should be able to meet people where they already work.

Just as importantly, the runner writes both successful and unsuccessful CSVs, along with failed-query and unparsed-row artifacts. That means gaps in extraction remain visible to reviewers instead of disappearing into silent attrition.

## A simple mental model for the output layer

Here is an easy way to explain the design:

**The model gives a response. The system turns that response into a business artifact.**

That transformation step is where a lot of production quality lives.

## What to copy in your own system

If you are building a similar workflow, make sure your output layer includes:

1. a stable schema
2. raw-output retention
3. parser-level normalization
4. success and failure separation
5. a downstream-friendly publish format

Without those pieces, even a good agent will feel unreliable over time.

## What comes next

Once the output contract is stable, the next question becomes operational:

How do you know the system is performing well, and how do you recover when it does not?

That is what Part 7 covers.
