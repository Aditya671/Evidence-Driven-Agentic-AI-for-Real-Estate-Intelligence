# System Prompts as Operating Policy — Cloud-Agnostic Edition

## Prompt responsibility

A production prompt should describe how the workflow behaves, not merely ask an LLM to "be smart."

It should define:

- available capabilities;
- tool selection expectations;
- evidence requirements;
- output contract;
- failure states;
- escalation rules;
- prohibition against unsupported claims.

## Prompt versus mapping

Keep stable operating policy in the system prompt.

Keep changing domain knowledge in governed mappings.

### System prompt

Controls:

- workflow behavior;
- tool sequencing;
- evidence policy;
- output rules;
- failure handling.

### Mapping

Controls:

- metric definition;
- aliases;
- keywords;
- source hints;
- extraction rules;
- conflict rules.

This separation is essential for portability because cloud migration should not require rewriting domain knowledge.

## Provider-neutral model boundary

Treat model inference as:

```text
ModelProvider.generate(
    system_policy,
    context,
    structured_input,
    output_schema
)
```

The application should not depend on one vendor's prompt API.

## Structured output

Prefer schema-constrained output where supported, then validate independently.

Never treat model compliance as validation.
