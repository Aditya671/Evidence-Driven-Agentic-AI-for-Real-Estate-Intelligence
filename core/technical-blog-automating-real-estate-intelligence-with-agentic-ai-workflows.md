# Automating Real Estate Intelligence with Agentic AI Workflows

*A sanitized technical deep dive into how a production-oriented agentic AI system extracts high-value real estate intelligence from unstructured and semi-structured documents, using internal rate of return (IRR) and rent growth rate as concrete examples.*

## Introduction

The hardest part of building an agentic AI system for real estate is not attaching an LLM to a search index. It is building a workflow that can extract high-value analytical signals from messy, versioned, enterprise documents without collapsing into hallucination, inconsistency, or unreproducible outputs.

That problem becomes especially difficult when the target outputs are not simple fields such as asset name, city, or property type. In this write-up, I will use **internal rate of return (IRR)** and **rent growth rate** as the concrete examples because they capture the real challenge well: both can appear across different document types, under different labels, with multiple scenarios, time bases, and calculation contexts. Many of these values do not exist as single explicit fields. They must be discovered across narrative text, financial schedules, transaction binders, appraisal reports, underwriting materials, and management decks.

This blog post explains how the workflow solves that problem in a structured way.

To protect organizational integrity, this write-up is intentionally sanitized. It avoids exposing proprietary identifiers, internal storage structures beyond architectural patterns, or organization-specific operating details that are not necessary to explain the engineering design.

The focus here is the architecture: how deterministic controls are layered around a stochastic model to make extraction reliable enough for production use.

## 1. The Architecture of Precision

### The Mapping JSON Is the Brain of the System

At the center of the workflow is a governed metric mapping JSON file.

This mapping contains a library of variable definitions. Each definition is not just a label. It is a compact control program for how the system should interpret, search for, validate, and normalize a variable.

Each variable entry includes these core metadata dimensions:

* `name`
* `category`
* `unit`
* `description`
* `keywords`
* `avoid`
* `types`
* `extraction_rules`

From a systems perspective, this can be thought of as **seven descriptive metadata controls plus one executable rule layer**.

The descriptive metadata provides semantic grounding:

* `name`, `category`, and `unit` tell the system what the variable is.
* `description` defines the business meaning of the variable.
* `keywords` provide positive search anchors.
* `avoid` provides explicit negative filters to reduce false positives.
* `types` defines the classifier structure for the variable.

Then `extraction_rules` turns the mapping from a glossary into an execution plan.

This is the critical design move.

Most AI extraction systems stop at semantic labeling. This one goes further and encodes how the variable should actually be found and adjudicated.

For public explanation, IRR and rent growth rate are especially useful examples:

* IRR shows how the system handles return metrics with scenario context, timing context, and multiple classifier variants.
* Rent growth rate shows how the system handles forecast assumptions, schedule-based evidence, and multi-period extraction logic.

Using the same architecture, the workflow can be extended to extract as much additional real estate data and information as the business needs.

### Why This Matters in a Stochastic Environment

LLMs are probabilistic. Enterprise extraction cannot be.

That does not mean the model becomes deterministic. It means the *workflow around the model* is made deterministic enough to constrain behavior.

The mapping file does this by reducing the model's freedom in several ways:

* it narrows the search space
* it defines acceptable synonyms
* it excludes misleading local context
* it binds values to classifier logic
* it instructs the workflow how to resolve ambiguity

For example, a variable such as IRR or rent growth rate does not merely say "find a number." Its mapping describes:

* which document families matter most
* which tables should be checked first
* which phrases in narrative text are acceptable evidence
* which nearby contexts must be rejected
* how to treat multiple plausible values
* how to prefer stronger sources over weaker sources
* how to validate timing and scenario context

That is not prompt fluff. That is extraction governance.

### `types` Is the Variable's Internal Ontology

The `types` array acts as a variable-specific ontology. Each type entry includes:

* `classifier`
* `description`
* `category`
* `formula`
* `unit`
* `subtypes`
* `column_name`

This matters because real estate variables are rarely flat.

For example, both IRR and rent growth rate can have multiple valid interpretations:

* underwriting versus current versus realized
* annual versus quarterly versus schedule-based
* base case versus downside versus upside
* explicit values versus values derived from surrounding evidence

Without classifier-aware logic, the system would either collapse distinct meanings into one answer or return inconsistent results depending on phrasing. By carrying classifier metadata directly in the mapping, the system can generate retrieval plans and output rows that preserve semantic distinctions.

### `extraction_rules` Encodes Conflict Resolution Logic

One of the strongest features in the mapping file is that the extraction logic is authored in a repeatable, ordered pattern.

The authoring approach per variable is:

1. **Where to look first**
2. **How to extract**
3. **How to calculate or validate**
4. **How to resolve conflicts**

This gives each variable an explicit search-and-decision recipe before the LLM ever starts retrieving.

That ordered authoring pattern can also be summarized as:

**Look -> Extract -> Validate -> Resolve**

That logic appears repeatedly across variables:

1. **Where to look first / Look**
   Restrict the search to the right document families, sections, tables, tabs, and evidence patterns.

2. **How to extract / Extract**
   Pull explicit values first from structured rows, headers, labels, and narrative statements.

3. **How to calculate or validate / Validate**
   Check the extracted value against plausibility, chronology, scenario context, units, and nearby negative cues.
   When an explicit value is missing, this is also where fallback calculations can be defined.

4. **How to resolve conflicts / Resolve**
   If multiple values remain, prefer authoritative versions, preserve classifier distinctions, and surface conflicts instead of averaging or silently overwriting.

This is a major reason the workflow scales to difficult variables. It does not ask the model to "be smart." It gives the model a procedure.

For example:

* for **IRR**, "where to look first" might prioritize return summaries, underwriting outputs, IC materials, and valuation sections
* for **rent growth rate**, "where to look first" might prioritize assumptions tabs, leasing schedules, forecast tables, and market-rent sections

Then the remaining parts of the rule define how to pull the number, how to validate or derive it, and how to resolve conflicts across sources or scenarios.

### The Query Generator Agent's Role

Before a variable reaches the main workflow agent, it is contextualized by a dedicated query generation step in:

* `the extraction runtime's query generation layer`

The query builder component transforms one mapping entry into a structured search specification containing:

* concept description
* scope of search
* targeted labels and synonyms
* classification and calculation logic
* internal output-mapping metadata

This is a crucial intermediary layer.

The query generator does not perform extraction. It performs **context compression**.

It converts a dense variable definition into an optimized retrieval brief that the main agent can execute consistently. That means the workflow does not begin with a vague prompt like "find IRR" or "find rent growth." It begins with a fully contextualized variable-specific search contract.

There is another practical implementation detail here that is easy to miss but important in production: the runner generates the metric-level search brief once, caches it, and then substitutes the target asset name at execution time. That keeps the retrieval framing consistent across the run instead of letting each asset call drift into slightly different prompt wording.

In practice, this improves:

* recall for difficult variables such as IRR and rent growth rate
* consistency across assets
* classifier fidelity
* downstream traceability in `rewritten_query`

## 2. The Multi-Tool Intelligence

The system does not rely on one tool to solve every retrieval problem. It orchestrates specialized tools, each handling a different layer of evidence discovery.

The current tool chain is also more explicit than the earlier path/chunk/table summary. It uses asset alias normalization, path search, doc-id scoped chunk filtering, controlled global fallback retrieval, and table validation. That sequence keeps evidence local to the selected source whenever possible and records the fallback path when the primary source path is not good enough.

This separation of responsibilities is one of the main reasons the workflow remains controllable.

### `document_path_tool`: Logical Document Filtering

At the top of the workflow is the document path layer.

In the implementation pattern, this logic is represented through path-index tooling and a path-first orchestration policy.

* the multi-index retrieval component
* the package-local metric extractor prompt artifact
* the retrieval service contract

The role of the document path tool is not to extract content. Its role is to narrow the candidate file set using path-level semantics such as:

* document family
* folder context
* asset naming
* version patterns
* recency hints
* sibling file discovery

This is a logical filter, not a semantic reader.

That distinction matters because enterprise corpora are noisy. If the workflow jumps directly into chunk-level retrieval across the entire corpus, false positives grow quickly. The path tool acts as a first-pass gate that says:

"Which files are even worth reading?"

### `chunk_retriever_tool`: Semantic Search

Once the candidate document set has been narrowed, the workflow pivots into content retrieval.

This is where semantic and hybrid search become useful.

The chunk retriever is responsible for:

* finding relevant passages
* surfacing table-adjacent text
* returning citations and page-linked evidence
* binding content back to `doc_id`, `file_path`, and metadata

It is the bridge between file discovery and value extraction.

This separation between path search and chunk search is important enough to highlight explicitly:

* the path layer answers **which file**
* the chunk layer answers **which evidence inside that file**

That dual-step design is cleaner and more auditable than trying to make one retrieval surface do both jobs.

### `data_table_visualization_tool`: Visual First for Financial Reports

The third tool is where the workflow becomes especially strong for real estate and finance.

Structured financial evidence often lives in:

* appraisal tables
* rent rolls
* sources and uses schedules
* operating statements
* valuation summaries
* underwriting model exports

These are not reliably handled by pure chunk text alone. OCR noise, PDF extraction errors, and flattened table structure can all degrade precision.

That is why the system prompt implements a **Visual First** priority for structured financial documents.

The rule is simple:

* use text retrieval to find the right page and context
* escalate to page/table visualization when the evidence is likely table-dominant

The workflow agent recognizes structured data patterns such as:

* repeated numeric columns
* common financial headers
* row/column label grids
* page-level references tied to PDF evidence

When those conditions are met, the table-oriented tool is used to confirm:

* the exact row and column
* the date context
* the unit and scaling
* the correct classifier or scenario label

This is a major anti-hallucination pattern. Instead of letting the model infer a number from degraded text, the workflow pushes it toward the visual structure that humans would also trust.

## 3. The Chain of Logic in System Prompts

### Why the Prompt Is About Procedure, Not Prose

The main workflow prompt is documented in the workflow prompt guide:

* the package-local metric extractor prompt artifact

This prompt is best understood as a policy engine, not a writing template.

Its job is not to make the model sound impressive. Its job is to control the workflow.

That is why the prompt prioritizes:

* retrieval policy
* tool ordering
* normalization rules
* metadata rules
* no-data handling
* file-version preference
* table escalation conditions
* output schema discipline

This is what a production system prompt should do.

### Retrieval, Normalization, and Metadata Rules Come First

The system prompt explicitly prioritizes the mechanics that keep extraction safe:

* path-level narrowing before chunk retrieval
* bounded tool calls
* metadata preservation
* page and table validation
* date normalization
* source traceability

This is important because the workflow is not really a text generation problem. It is a controlled evidence-construction problem.

The final answer is expected to be auditable, not merely plausible.

### Mission-Critical Tool Use Policy

The tool policy is one of the most important anti-hallucination controls in the entire system.

It prevents the agent from behaving like an unconstrained browsing assistant by enforcing:

* call order
* tool-specific roles
* usage limits
* escalation conditions
* schema-preserving fallback behavior

That policy matters because hallucination in enterprise extraction often comes from workflow drift, not only from model drift.

If the agent is free to over-search, over-summarize, or improvise around missing evidence, even a good model will eventually produce unverifiable outputs. The tool policy narrows that behavior envelope.

The workflow prompt also adds several operational safeguards that are especially valuable in enterprise extraction:

* it requires the workflow to record the actual retrieval trail in `rewritten_query`, including both path-level file-targeting queries and content-level evidence queries
* it forces the model to distinguish chunk-only extraction from table-confirmed extraction through `has_table` and `output_level`
* it defines a schema-preserving no-data behavior so failed extraction attempts still produce a controlled, reviewable output row instead of collapsing the batch into silence

Those details matter because they make the workflow explainable after the fact, not just during prompt design.

### Prompt Fidelity Was Preserved Deliberately During Modularization

One of the strongest engineering choices in this architecture is that the production prompts were not casually rewritten as the workflow system was packaged more formally.

In `the metric extraction runtime`, the runtime keeps the production prompt artifact package-local and refreshable through the prompt export path. That means the modular workflow intentionally preserves proven workflow behavior instead of approximating it from memory.

This is a subtle but very important production lesson:

**prompt packaging should preserve behavior first and beautify later, if at all.**

### Company Knowledge Base vs Storage Account

A subtle but important architectural split exists between:

* the **knowledge base** used for indexed retrieval
* the **raw storage layer** that holds the underlying source files

The knowledge base, backed by indexed and searchable representations, is where the agent performs fast retrieval, chunk search, path filtering, and evidence localization.

The storage layer remains the source of truth for:

* raw file integrity
* exact binary content
* version-adjacent sibling inspection
* fallback extraction when indexed content is incomplete or mismatched

This distinction supports cross-validation.

In fact, the MCP documentation explicitly calls out a known path-chunk `doc_id` mismatch case and recommends fallback to raw blob inspection or direct PDF extraction when the index and content surfaces disagree.

That is an important production design lesson:

**the index is the retrieval surface, but the raw file layer is still the final integrity anchor.**

## 4. Data Transformation Pipeline

The system does not stop when the model produces a response. It moves each extracted signal through a controlled transformation pipeline.

The path looks like this:

**Raw Document -> Lean JSON Extraction -> Enrichment -> Normalized JSON -> CSV and Monitoring Outputs**

### Step 1: Raw Document

The source documents may be:

* PDFs
* decks
* financial reports
* appraisals
* IC memos
* underwriting outputs

These are indexed and retrieved through the search and tooling layers, but they remain the origin of truth.

### Step 2: Lean JSON Extraction

The current workflow instructs the model to emit a lean accepted-value JSON result under a `results` envelope.

The runtime preserves compatibility with older markdown-style outputs, but the active path is JSON-first.

The lean JSON row is used for a practical reason:

it keeps source acceptance focused on the selected value and same-source lineage before business-context enrichment begins.

That is especially useful when the output contains:

* multiple rows
* repeated evidence fields
* semicolon-joined metadata
* classifier-separated values
* fallback comments

### Why Enrichment Is a Separate Stage

Enrichment is not cleanup. It is a separate control layer.

It helps maintain integrity between evidence acceptance and business-ready output because it:

* adds reporting basis, time basis, value subtype, and value scale after source validation
* separates accepted evidence from display normalization
* marks table-confirmed rows only when table evidence was actually used
* records whether a value was direct, derived, allocated, reconciled, confirmed, not found, or not applicable

In other words, enrichment is the bridge between stochastic evidence extraction and deterministic business reporting.

### Step 3: JSON Parsing

Once lean JSON or legacy markdown output is produced, it is normalized into structured JSON using:

* `the extraction runtime's parser and formatter layer`

This parser and formatter layer does more than parse one shape. It includes support for:

* row-width repair
* escaped-pipe handling
* tail alignment recovery
* field normalization
* structured record generation

This is critical because production systems evolve. The parser acts as a containment layer that keeps older outputs usable while the active runtime moves toward a cleaner JSON-first contract.

In practice, the parser and normalization layer protects the workflow from historical format drift, while enrichment agents add the current business-ready fields.

### Step 4: CSV Storage

The final structured records are exported by the workflow runner in:

* `the extraction runtime's batch runner`

The runner persists outputs across multiple layers:

* active `json_format` outputs
* retry archives under `json_format_archive`
* successful and unsuccessful CSVs
* extraction monitoring tables
* status reports, failed-query artifacts, and upload manifests

This layered design supports:

* auditability
* replay
* parser debugging
* business consumption

The runner also separates outcomes into successful and unsuccessful CSV outputs while writing detailed failed-query and unparsed-row artifacts alongside them. That is exactly the kind of operational detail that makes a production extraction system maintainable: failures are preserved as first-class data, not hidden as missing rows.

The CSV is not a downgrade from AI output. It is the business-ready contract surface.

## Why This Architecture Works

The reason this workflow is effective is that it does not treat the LLM as the whole system.

Instead, it composes:

* a variable brain in JSON mappings
* a query contextualization layer
* a path-first retrieval strategy
* semantic chunk search
* visual-first table validation
* prompt-based operating policy
* parser-based normalization
* layered output persistence

That architecture is what allows a stochastic model to behave with production discipline.

## Final Takeaway

If you want to automate real estate intelligence at scale, the key is not simply choosing a larger model or a better vector database.

The key is building a workflow where:

* the variable definitions are precise
* the search plan is contextualized
* the tools have distinct responsibilities
* the prompt enforces policy
* the parser enforces structure
* the storage layers preserve integrity

That is how this system can reliably extract difficult metrics such as **IRR** and **rent growth rate** from unstructured and semi-structured sources while still preserving evidence, classification, and auditability. Using the same approach, the workflow can be extended to extract as much additional real estate data and information as required.

In short:

**the intelligence is not only in the model. It is in the architecture around the model.**
