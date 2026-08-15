# Building an Evidence-Driven Agentic AI System for Real-Estate Intelligence

A production-oriented reference architecture for extracting trustworthy, traceable intelligence from complex enterprise documents.
```
Business Question
       ↓
Entity Resolution
       ↓
Domain Grounding
       ↓
Agentic Planning
       ↓
Evidence Retrieval
       ↓
Extraction
       ↓
Validation
       ↓
Conflict Resolution
       ↓
Evidence-Backed Output
```
---
This folder contains a Medium-ready series about building a production-grade agentic AI system for enterprise document intelligence. The series is intentionally sanitized: it explains the architecture, workflow patterns, extraction discipline, and operating model without exposing private source names, tenant-specific paths, proprietary identifiers, or sensitive prompt text.

The goal of the series is simple:

* help a technical reader understand how a real agentic AI system is assembled
* make the ideas approachable for product, data, and engineering readers
* stay grounded in production implementation patterns
* focus on an Azure-centered production system

## Flagship Standalone Post

This series also includes a standalone technical deep dive built from the blog brief:

* Subject: `Automating Real Estate Intelligence with Agentic AI Workflows`
* Focus: using internal rate of return (IRR) and rent growth rate as concrete examples to explain how the same workflow can extract as much real estate data and intelligence as needed
* Draft: `technical-blog-automating-real-estate-intelligence-with-agentic-ai-workflows.md`

Use that post as the single-article version of the story, and use the numbered parts as the expanded series.

## What This Series Is Trying To Teach

This is not a series about "how to make an LLM call tools."

It is a series about how to build a system that can be trusted to answer business questions from messy enterprise documents.

That means the series is built around five repeated themes:

1. Retrieval quality comes before agent cleverness.
2. Domain grounding matters as much as model choice.
3. Prompts are operating policies, not writing exercises.
4. Raw model output is not the final product.
5. Evaluation, recovery, and governance are part of the architecture.

## Extraction Rule Authoring Pattern

One of the core ideas behind this system is that extraction rules are written in a repeatable per-variable pattern.

For each variable, the mapping logic is authored in this order:

1. **Where to look first**
   Define the highest-priority document families, sections, tabs, tables, or page contexts.

2. **How to extract**
   Define the labels, synonyms, structural patterns, and narrative cues that count as evidence.

3. **How to calculate or validate**
   Define fallback formulas, plausibility checks, unit checks, chronology checks, and scenario checks.

4. **How to resolve conflicts**
   Define source precedence, version precedence, classifier handling, and what to do when multiple plausible values remain.

This pattern is a major reason the workflow stays disciplined. It turns a vague extraction target into a controlled retrieval-and-decision recipe.

## Implementation Areas Used In The Writing

The public narrative refers to implementation areas rather than private repository paths:

* Metric extraction runtime
* Tool description layer
* Query generation layer
* Path-index and chunk-index retrieval tools
* Parser and formatter layer
* Runtime configuration layer
* Governed metric mapping layer
* Sanitized asset and investment reference datasets
* Application platform data-indexing modules
* Conversational service layer
* Index-update pipeline
* Application platform documentation

## Current System Lens

The updated series treats the production system as two connected layers:

* a general Azure document intelligence platform for ingestion, indexing, retrieval, chat, user uploads, and observability
* a specialized metric extraction workflow for governed asset-level data extraction, enrichment, monitoring, and export

The public narrative stays sanitized: it explains the architecture patterns without exposing proprietary identifiers, secrets, tenant-specific paths, or sensitive prompt text.

## Sanitization Policy

All documents in this folder are intentionally sanitized to protect organizational integrity.

That means the series:

* avoids exposing proprietary identifiers, tenant-specific values, and operational secrets
* avoids reproducing sensitive prompt material beyond what is necessary to explain architecture
* focuses on engineering patterns, control logic, and workflow design rather than organization-specific business detail
* refers to document families, storage layers, mapping structures, and tool roles at an architectural level

This keeps the writing safe to share publicly while still making it technically useful for AI engineers, data scientists, and PropTech readers.

## Series Map

1. `part-01-foundation-before-agents.md`
   Why the real foundation is indexing, metadata, and corpus trust.

2. `part-02-retrieval-architecture-that-agents-can-trust.md`
   Why this system separates path discovery from chunk retrieval.

3. `part-03-domain-grounding-mappings-and-entity-resolution.md`
   How mappings, aliases, and entity hierarchy turn search into understanding.

4. `part-04-system-prompts-as-operating-policy.md`
   How the system prompt controls behavior, risk, and output discipline.

5. `part-05-turning-an-agentic-ai-workflow-into-a-maintainable-system.md`
   How to turn a successful agentic AI workflow into a maintainable workflow package.

6. `part-06-data-contracts-post-processing-and-business-ready-outputs.md`
   Why the final product is not a model response but a governed business artifact.

7. `part-07-evaluation-observability-and-recovery.md`
   How to measure quality, monitor runs, and recover safely.

8. `part-08-security-governance-and-how-to-roll-it-out.md`
   How to make the system adoptable inside a real organization.

[System](linkedin-cover.png)

* one sanitized architecture diagram
* one sanitized sequence diagram
* one short pseudocode excerpt for tool orchestration
* one sanitized mapping snippet
* one sanitized asset-data example
