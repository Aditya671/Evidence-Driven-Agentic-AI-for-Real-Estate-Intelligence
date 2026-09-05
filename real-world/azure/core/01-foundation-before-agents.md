# Part 1: Before You Build Agents, Build the Ground They Stand On

When people say they want to build an agentic AI system, they usually start with the visible part:

- the model
- the tools
- the orchestration loop
- the interface

That is understandable, but it is usually the wrong starting point.

If the system is meant to work in a real industry setting, the first thing to build is not the agent. It is the ground the agent stands on.

In the reference architecture, that ground is a governed Azure document corpus with indexing rules, metadata discipline, freshness controls, thread-aware upload handling, and traceable outputs. The implementation pattern spans a general application and retrieval platform plus a specialized metric extraction workflow.

This is the core message of Part 1:

**production-grade agentic AI begins with retrieval trust, not with prompt cleverness.**

## The human problem behind the architecture

Let us make this concrete.

Imagine an analyst asks:

"What is the current occupancy for this asset, and where did it come from?"

That sounds like one question. In reality, the system has to solve several smaller questions first:

- Which document family is likely to contain the answer?
- Is the asset name written the same way in every file?
- Which document version should count as current?
- Is the answer in narrative text or a financial table?
- Can the system prove the answer with a file path, page number, and citation?

This is why so many early agent demos feel magical but break in production. The visible question is simple. The hidden data work is not.

## What the reference architecture teaches about the real foundation

The architecture repeatedly shows that the system is built on top of a retrieval substrate, not just an LLM.

That shows up across several design layers:

- runtime configuration
- indexing design
- retrieval service design
- unstructured document processing
- metadata and freshness strategy
- incremental reindexing and change detection

Taken together, these sources describe a system that cares deeply about:

- document identity
- chunk identity
- version handling
- checksum-based change detection
- searchable metadata
- reproducibility of runs

That is not background plumbing. That is the actual foundation.

## Why indexing comes before orchestration

The indexing foundation makes a very important point:

- no extraction quality without retrieval quality
- no retrieval quality without indexing quality

This is one of the clearest truths in production AI.

If the corpus is weak, the agent is forced to compensate by guessing. And when a system guesses in a high-value workflow, it usually sounds more confident than it should.

Bad indexing shows up later as:

- missing evidence
- wrong document versions
- low recall
- duplicate or stale answers
- asset mix-ups
- poor auditability

That is why Step 0 in this system is not "build the agent." It is:

1. onboard documents
2. parse them
3. chunk them
4. embed them
5. store the right metadata
6. detect changes deterministically
7. validate the resulting corpus

That is the boring work that makes the exciting work possible.

## The metadata layer is doing more than you think

One of the most important ideas in this project is that every extracted answer should be traceable back to a real source object.

The documents and chunks are not treated as anonymous text. They carry business context through fields such as:

- `doc_id`
- `path`
- `checksum`
- `last_modified`
- `uploaded`

The freshness strategy takes this further by defining states like:

- new
- current
- stale
- failed
- deleted

This matters because enterprise AI is rarely just about finding text. It is about deciding whether that text is still trustworthy.

If your system cannot answer "is this still the current version?", it is not ready for production reporting.
## The mapping layer is part of the foundation too

Another important foundation element in this project is the way extraction rules are authored inside the mapping layer.

The system does not treat a variable as just a label to search for. Each variable is written as a small retrieval-and-decision recipe that follows a consistent order:

1. where to look first
2. how to extract
3. how to calculate or validate
4. how to resolve conflicts

That means the mapping file is not just metadata. It is part of the workflow logic itself.

This is a useful lesson for any production system: if the extraction method lives only in the model prompt, it becomes hard to govern. If it lives in structured mappings, it becomes easier to maintain, review, and extend.

## The layered data flow is a huge production clue

Another detail worth noticing is how output data is stored.

The metric extraction runtime uses a layered output pattern:

- raw inputs remain preserved
- active extraction outputs are stored as structured JSON under `json_format`, with archive and report folders for retry and audit
- processed outputs are normalized into successful, unsuccessful, monitoring, and master-data CSV files

That gives the team something extremely valuable:

- rerun safety
- parser debugging
- replayability
- audit support
- easier QA

This is a very human-centered design choice because it recognizes how teams actually work. People need to inspect, compare, replay, and explain results. A single opaque final answer is not enough.

## A simple mental model for readers

If you want an easy way to explain this architecture to someone outside the AI team, use this:

**The agent is not a genius researcher. It is a careful analyst working inside a well-organized evidence room.**

If the evidence room is messy:

- the agent wastes time
- the agent misses things
- the agent mixes things up
- the agent cannot show its work

If the evidence room is organized:

- the agent searches better
- the agent retrieves faster
- the agent cites correctly
- the outputs are easier to trust

That is the simplest way to understand why corpus engineering matters so much.

## What to copy if you are building your own system

If you are building an industry agentic-AI system in another domain, this first part suggests a clear checklist:

1. Decide what the unit of truth is.
   A document, case, claim, patient file, transaction, property, or machine record.

2. Define a metadata contract early.
   You need identity, timestamps, checksum or version semantics, and a way to mark current versus historical state.

3. Make indexing replayable and deterministic.
   Unchanged files should be skipped cleanly. Changed files should be reprocessed cleanly.

4. Keep raw and processed layers separate.
   This is how you debug and improve safely.

5. Validate the corpus before you trust the agent.
   Do not push retrieval problems upward into prompts.

## What comes next

Once the corpus is trustworthy, the next real design question becomes:

**How should the agent search it?**

That is what Part 2 covers. The answer in this project is especially good: the system separates file discovery from content retrieval by using both a path index and a chunk index. That single choice makes the rest of the workflow much more precise and much easier to audit.














