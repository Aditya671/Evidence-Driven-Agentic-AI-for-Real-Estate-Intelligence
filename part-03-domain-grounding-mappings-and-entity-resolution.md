# Part 3: Domain Grounding Is What Turns Search Into Understanding

Good retrieval is necessary, but it is not enough.

An agent can find relevant text and still produce the wrong answer if it does not understand:

* what the business term actually means
* which aliases refer to the same entity
* which entities are parent-level versus child-level
* which metric variants should be treated separately

This is where the metric extraction runtime becomes especially instructive. It does not just search documents. It grounds search and extraction in domain-aware data assets.

## The human problem here is ambiguity

A user may ask for:

* the asset
* the investment
* the building
* the address
* the fund-level context

And they may use names that are:

* abbreviated
* old
* informal
* operational rather than legal

At the same time, a requested metric such as rent growth, NOI, vintage date, or occupancy might appear:

* under multiple labels
* in multiple scenarios
* in multiple document families
* with multiple classification types

Without grounding, the agent has to improvise. That is where mistakes happen.

## What grounding artifacts exist in the system

The system uses several grounding assets that reduce ambiguity before synthesis even begins:

* a key-variable extraction map
* a governed metric mapping JSON file
* sanitized asset reference data
* sanitized investment reference data
* reusable domain skills and retrieval policies

These are not side documents. They are part of the system's intelligence.

## The asset master makes search more anchored

The asset CSV is a good example.

It does not only store asset names. It also stores:

* fund
* investment
* address
* city and state
* P-codes
* property type
* latitude and longitude

That means the system can search using richer anchors than a single user phrase.

This matters because enterprise data is messy. A name in a request is often not the exact name in the document.

## Alias knowledge is quietly essential

The fund alias file and the `fund_knowledge_str` prompt artifact solve another very real problem:

organizations rarely speak with one stable naming system.

The same fund or product may appear as:

* a formal name
* a legacy name
* an internal shorthand
* an older brand label

If the agent does not know those relationships, retrieval becomes inconsistent and the answers feel random to users.

This is one of the best reasons to maintain explicit alias knowledge outside the model weights.

## Entity hierarchy matters more than most teams expect

The `pere-entity-hierarchy` skill captures something extremely important:

the requested entity level and the matched document level are not always the same.

For example:

* a user may ask about an asset
* the best source may be an investment-level memo
* that memo may still be valid if it clearly covers the requested asset

This sounds subtle, but it is a major production issue. If you do not model this explicitly, the agent either rejects useful evidence or overstates confidence.

That is why the skill emphasizes concepts such as:

* requested entity level
* matched document level
* coverage scope
* temporal scope
* ambiguity notes

This is a mature design choice because it teaches the system to be honest about scope.

## Metric mappings act like search control panels

The metric mapping files also do much more than rename variables.

They define:

* descriptions
* units
* keywords
* avoid terms
* classifier logic
* subtypes
* formulas
* likely column names

This gives the system a controlled way to understand what the user really means.

Instead of saying "search for rent growth," the system can reason more carefully:

* which subtype?
* which classification?
* which document family?
* which nearby terms are positive signals?
* which terms are likely false positives?

That is how a retrieval pipeline starts to become domain-aware.

## How to author extraction rules in the mapping layer

One of the most practical lessons in this project is that each variable should be written as a small decision workflow, not just a name plus keywords.

The authoring pattern is:

1. where to look first
2. how to extract
3. how to calculate or validate
4. how to resolve conflicts

That pattern matters because it turns the mapping into a reusable operating asset.

For example, for a variable such as IRR or rent growth rate:

* **Where to look first** defines the most likely document families, sections, tables, schedules, or tabs.
* **How to extract** defines the accepted labels, synonyms, classifier hints, and structural cues that count as evidence.
* **How to calculate or validate** defines fallback formulas, unit checks, chronology checks, and scenario checks.
* **How to resolve conflicts** defines source precedence, version precedence, and what to do when multiple plausible values remain.

This is the simplest way to keep a stochastic workflow disciplined. The model is still doing retrieval and interpretation, but the mapping tells it what good evidence looks like and how to behave when the evidence is messy.

## A simple mental model for grounding

Here is an easy way to explain the purpose of these assets:

**The indexes help the agent find information.**
**The mappings help the agent understand what it is looking at.**

Both are necessary.

## What to copy in your own system

If you are building this pattern in another domain, your grounding layer should probably include:

1. an entity master
2. alias maps
3. metric or intent definitions
4. hierarchy and fallback rules
5. document-family hints

Without those, even good search tends to stay shallow.

## What comes next

Once the system has retrieval structure and domain grounding, the next question is:

**Where do all the operating rules live?**

In this project, a large part of that answer is the workflow system prompt. Part 4 shows why the prompt is best understood as a policy document, not a clever piece of prose.
