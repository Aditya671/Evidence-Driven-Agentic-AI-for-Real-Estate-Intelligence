# Part 7: If You Cannot Measure and Recover, Your Agent Is Not Production-Ready

Most teams spend the majority of their time building the retrieval and orchestration logic.

But once the system starts running on real workloads, a different question becomes more important:

How will you know when it is getting worse?

And just as important:

What happens when it fails in the middle of a run?

The metric extraction runtime, along with the supporting PoC docs, provides a practical answer: treat evaluation, observability, and recovery as core parts of the architecture, not support functions.

## Evaluation should be tied to business truth

The data-quality framework lays out a strong baseline:

- field accuracy
- citation completeness
- coverage
- false positive rate

Those are the right kinds of measures for an extraction workflow.

They ask the questions a business user actually cares about:

- Did we get the value right?
- Did we keep the evidence?
- How often do we return a usable answer?
- How often do we confidently return the wrong thing?

That is much more useful than generic model benchmark talk.

## Golden datasets keep you honest

The evaluation docs recommend a golden dataset with representative examples and evidence anchors.

That is essential because production changes come from many places:

- prompt updates
- retrieval updates
- mapping updates
- parsing updates
- index refresh behavior

Without a stable evaluation set, a team can change the system and only discover weeks later that one category of answers silently got worse.

## Observability must be stage-aware

The observability runbook is strong because it does not just ask for logs. It asks for stage-aware telemetry.

That includes things like:

- run start and end
- scanned, indexed, skipped, and failed counts
- requested, succeeded, no-data, and failed extraction counts
- token and latency metrics
- freshness lag
- failure-category breakdowns

This is the right pattern because agentic systems are pipelines, not monoliths.

## Recovery should be built into the workflow

The recovery strategy pairs well with what the runner already does in the workflow.

The system is designed to:

- continue when one item fails
- persist enough context for replay
- isolate the failed subset
- distinguish between failure categories

This is an excellent production habit. Large extraction runs should not need full reruns every time something breaks.
The current metric extractor makes this concrete through extraction monitoring. It maintains latest-state coverage at asset, metric, and classifier grain, records skip, retry, fallback, and retrigger events in JSONL form, and separates active outputs from archive diagnostics. That gives teams a way to answer not just "did the run finish?" but "which work was skipped, retried, accepted, unresolved, or blocked by source-lineage checks?"

## Error categories matter

One of the more mature ideas in the PoC docs is the error taxonomy:

- input or parse issues
- retrieval issues
- LLM or agent issues
- output validation issues
- platform issues

Different failures deserve different responses.

That sounds obvious, but many AI systems still treat every error as the same kind of exception.

## What to monitor in your own system

If you are building a similar platform, the most useful things to monitor first are usually:

- freshness
- retrieval coverage
- successful row coverage
- citation completeness
- parse failure rate
- no-data rate
- stage latency
- failure rates by category
- retry and fallback reasons
- classifier coverage by metric
- source-lineage validation failures

Even a simple dashboard around those signals will help a lot.

## A simple mental model for production reliability

Here is the easiest way to explain this part:

**A reliable AI system is not one that never fails. It is one that fails visibly, predictably, and recoverably.**

That is the mindset shift that makes operations much healthier.

## What comes next

The final step in the series is the organizational layer.

Even if the system works technically, it still has to be trusted, governed, and handed over responsibly.

That is what Part 8 covers.






