# Part 4: Treat the System Prompt Like an Operating Policy, Not a Clever Paragraph

There is a big difference between a prompt that sounds smart and a prompt that runs a system safely.

In production agentic AI, the prompt should do the second job.

The metric extraction workflow prompt is a strong example of this. It is not written like a branding statement. It is written like an operating policy for a controlled evidence-extraction workflow.

## Why this distinction matters

In many AI projects, prompts are treated like soft guidance. They are adjusted casually, discussed informally, and rarely tested as system artifacts.

That is risky.

In this architecture, the main workflow prompt effectively governs:

- how tools are called
- what order they are called in
- how many times they can be called
- when to escalate to table inspection
- how dates should be normalized
- how versions should be chosen
- how no-data responses should behave
- what fields must exist in the output

That is not copywriting. That is system behavior.

## What the workflow prompt is really doing

The workflow prompt documentation makes the structure very clear.

It covers:

- role and mission
- tool use policy
- output schema
- no-data contract
- visual-first handling for structured financial PDFs
- query and retrieval strategy
- metadata rules
- file-version rules
- advanced date normalization

This is why I describe it as an operating policy. It is the place where the system's practical rules are collected and made explicit.

## What belongs in the mapping layer versus the system prompt

One useful way to understand this workflow is to separate two different responsibilities:

- the mapping layer authors the extraction rule for each variable
- the system prompt tells the agent how to execute those rules safely

That division of labor is important.

The mapping layer defines the per-variable recipe:

1. where to look first
2. how to extract
3. how to calculate or validate
4. how to resolve conflicts

For a variable such as IRR or rent growth rate, the mapping should say which document families matter first, which labels or schedule patterns count as evidence, which calculations or checks are acceptable, and how to decide between competing values.

The system prompt then turns those authored rules into runtime behavior. It governs:

- tool order
- search discipline
- normalization requirements
- citation and metadata handling
- no-data behavior
- escalation from text retrieval to table inspection

This is a very strong production pattern because it keeps business logic and execution policy separate. The mapping says what a variable needs. The prompt says how the agent must behave while pursuing it.
The same policy idea now extends beyond the main extraction prompt. Post-extraction agents handle scale, reporting basis, time basis, value subtype, table evidence, and value resolution. That keeps the parent workflow focused on accepted evidence while specialized enrichment steps normalize context after the row has passed source-lineage checks.

## Constrain first, then empower

One of the best design choices here is that the prompt does not simply tell the agent to be helpful. It gives the agent bounded freedom.

Examples include:

- path-first narrowing
- bounded retriever calls
- bounded table-tool calls
- fixed output structure
- explicit no-data fallback behavior

This matters because enterprise systems need consistency more than theatrical reasoning.

The goal is not to let the agent roam. The goal is to let it search intelligently inside a safe operating envelope.

## The prompt and the parser are designed together

Another smart pattern in this system is that the prompt is tightly connected to downstream parsing.

The agent is instructed to return a strict `results` structure containing markdown rows with a fixed schema. That structure then feeds the parser and finally the processed CSV output pipeline.

This means the prompt is not separate from the data pipeline. It is one stage in the data pipeline.

That is an important mindset shift for production teams.

## Prompt fidelity is protected during system packaging

The workflow also avoids a very common systems mistake: manually rewriting working prompts during refactoring.

The current metric extractor keeps production prompts as package-local artifacts under `package-local prompt artifacts` and refreshes them through the prompt export path when the source workflow changes.

That gives the team:

- workflow parity
- clearer source of truth
- safer workflow packaging
- testable prompt loading behavior

If your system still has prompt logic spread across early implementation surfaces, this is an excellent pattern to copy.

## A simple way to explain a production prompt

If you need a plain-English explanation, use this:

**A production prompt is a rulebook for how the agent should behave when evidence is messy, expensive, or incomplete.**

That framing helps non-LLM specialists understand why prompt design matters so much.

## What to include in your own production prompt

At a minimum, a production workflow prompt should define:

1. mission and boundaries
2. tool policy
3. evidence requirements
4. output contract
5. normalization rules
6. fallback behavior

The key is not length. The key is that the instructions match real operational risks.

## What comes next

Once the prompt acts like a reliable operating policy, the next challenge is organizational and technical:

**How do you move from a successful agentic AI workflow to a package you can maintain?**

That is what Part 5 covers.








