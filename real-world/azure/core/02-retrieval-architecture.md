# Part 2: The Retrieval Architecture That Makes Agents Useful

After the corpus is built, the next question is not "which model should we use?"

The next question is:

**How should the system search for evidence without getting lost?**

The answer in this reference architecture is a strong one. Instead of asking one retrieval layer to do everything, the system uses two distinct search surfaces:

- a path index for file discovery
- a chunk index for content retrieval

That is a small architectural decision with big downstream benefits.

## The human version of the problem

Suppose a user asks:

"Find the underwriting rent growth assumption for this asset."

A person would not immediately skim random text chunks across the entire corpus. A careful person would first ask:

- Which document is likely to contain this?
- Is it an IC memo, a model export, an appendix, a reporting deck?
- Which file version is most relevant?

Only after that would they read the actual content.

That is exactly what this system teaches the agent to do.

## What the two-index pattern looks like here

The architecture uses a dual-index setup:

- file and folder discovery through the path index
- semantic and keyword evidence retrieval through the chunk index

The pattern is implemented through:

- path-level file and folder discovery
- chunk-level semantic and keyword retrieval
- multi-index retrieval orchestration
- architecture-level data-flow controls
- sequence-level evidence tracing

The path side helps answer:

- which file
- which folder
- which version
- which related sibling files

The chunk side helps answer:

- which passage
- which table context
- which page
- which exact evidence snippet

That separation reduces chaos.

## Why one retrieval layer is usually not enough

A single vector index sounds elegant, but it often forces the system to do too much in one jump.

It has to infer:

- the right document family
- the right version
- the right content location
- the right interpretation

That can work in a toy setup. In an enterprise corpus, it becomes noisy quickly.

The two-index approach is better because it breaks retrieval into a more human sequence:

1. narrow the search space
2. inspect the likely evidence
3. escalate only when needed

That is a much more stable way to build an agent.

## How the workflow behaves in practice

The default retrieval rhythm in this project is:

1. use a path tool to find likely candidate files
2. use a retriever tool to search within those candidates
3. use a table or page-level tool if structured evidence is likely

That policy appears in:

- metric extractor tool descriptions
- the `azure-search-core` skill
- the workflow system prompt
- the multi-index retrieval component

This design is both fast and readable:

- fast because it narrows before reading deeply
- readable because the system can explain which files led to which evidence

It also connects directly to how extraction rules are authored in the mapping layer. The first part of a good extraction rule is always:

**Where should the workflow look first?**

That usually means defining:

- the preferred document family
- the preferred section or table type
- the preferred evidence layout
- any fallback search scope

In other words, the retrieval architecture and the mapping architecture reinforce each other. The search stack works best when the extraction rules tell it where to start, before the workflow moves on to extraction, validation, and conflict resolution.
The current extractor makes this even more disciplined by separating chunk_filter_tool from chunk_retriever_tool. The chunk filter is used after file discovery so evidence stays tied to selected doc_id values. The broader retriever is still available, but it is treated as a controlled fallback rather than the default first move.

## Why path search is more valuable than it first appears

Many teams underestimate file-path search because it sounds less advanced than vector retrieval.

In practice, file paths often carry rich business signal:

- geography
- business unit
- document family
- asset grouping
- version hints
- final versus draft naming

The path index therefore becomes more than a file browser. It becomes a structured clue system.

The architecture can also add version discovery behavior so the agent can inspect related sibling files and nearby versions when that matters.

That is exactly the kind of practical intelligence production systems need.

## Why chunk retrieval still matters so much

Once the system has narrowed the file set, it still needs to answer the real question:

"Where is the evidence?"

That is where chunk retrieval earns its place.

A well-designed retrieval service supports:

- keyword search
- vector search
- hybrid search
- fetch-by-`doc_id`
- blob sibling exploration

This lets the system move flexibly between:

- exact term lookups
- fuzzy semantic retrieval
- targeted document reads
- broader document-neighborhood exploration

That is much more powerful than a single search function pretending to do it all.

## The table-escalation pattern is especially important

One of the strongest ideas in this system is that not all evidence should be read as plain text.

Financial and operational values often live in:

- schedules
- rent rolls
- operating statements
- appraisal tables
- model exports

That is why the workflow can escalate from chunk retrieval to a table or page-level inspection step when a PDF page looks authoritative for numeric extraction.

This is a very practical production pattern:

- start with text because it is fast
- move to page-level evidence when the value is likely table-bound

That is much closer to how a careful analyst works.

## A simple way to explain the architecture

If you need to explain this to a mixed audience, here is the simplest framing:

**The path index helps the agent find the right folder and file.**
**The chunk index helps the agent find the right paragraph or table.**

That one idea is easy to understand and captures the whole design.

## What to copy in your own domain

You do not need to be in real estate to reuse this pattern.

The same design works for:

- legal case files
- insurance claim packages
- healthcare patient records
- industrial maintenance archives
- procurement and contract repositories

In every case, the idea is the same:

- separate file discovery from evidence extraction
- preserve metadata during both steps
- let the agent move from coarse search to precise search

## What comes next

Even the best retrieval stack can still fail if the system does not understand the language of the business.

That is where Part 3 comes in. The next layer is domain grounding: mappings, aliases, entity hierarchy, and the rules that stop the agent from confusing one business object with another.











