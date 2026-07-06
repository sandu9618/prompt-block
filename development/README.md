# Development Prompt Blocks

Ready-to-use prompt patterns for software engineering architecture decisions, code review, debugging, refactoring pipelines, and AI features that need to integrate with production code. Each block includes a description, usage guidance, a copy-paste prompt template, worked examples, and tips for getting reliable output.

These prompts are written for developers, tech leads, and platform engineers who treat prompting as part of the engineering workflow, not a shortcut around thinking.

## When to use these

Reach for a development prompt block when:

- A single prompt produces inconsistent or incomplete engineering output
- You need reasoning you can audit before committing to a design
- Output must feed a linter, CI pipeline, or downstream automation
- You run the same transformation weekly and want format consistency
- A complex task needs to be broken into verifiable stages

For analytical work metrics, churn, competitive research, see [../analysis/](../analysis/). For content creation prompts, see [../writing/](../writing/).

## Available patterns


| Pattern               | Best for                                                                     | File                                           |
| --------------------- | ---------------------------------------------------------------------------- | ---------------------------------------------- |
| **Chain of Thought**  | Architecture decisions, production debugging, trade-off analysis             | [chain-of-thought.md](./chain-of-thought.md)   |
| **Few-Shot**          | Code transformation, commit messages, test generation, consistent formatting | [few-shot.md](./few-shot.md)                   |
| **Prompt Chaining**   | Multi-stage pipelines: understand → analyse → design → specify               | [prompt-chaining.md](./prompt-chaining.md)     |
| **Role Prompting**    | Code review, security audits, architecture reviews, domain-specific depth    | [role-prompting.md](./role-prompting.md)       |
| **Structured Output** | AI-powered linting, PR review automation, typed config generation            | [structured-output.md](./structured-output.md) |




### Quick guide to picking a pattern

- **You need to choose between options and justify the call** → start with [Chain of Thought](./chain-of-thought.md)
- **You want the same output shape every time** → start with [Few-Shot](./few-shot.md)
- **The task has distinct stages that shouldn't be compressed** → start with [Prompt Chaining](./prompt-chaining.md)
- **Domain expertise should shape the answer** → start with [Role Prompting](./role-prompting.md)
- **The output needs to be consumed by code, not read by a human** → start with [Structured Output](./structured-output.md)



## How to use a prompt block

1. **Open the pattern file** that matches your task.
2. **Copy the prompt template** from the `## Prompt` section.
3. **Replace the bracketed placeholders**, problem description, code, schema, constraints with your specifics.
4. **Read the example** to see what good output looks like for that pattern.
5. **Check Tips** before your first run, they cover the mistakes that produce plausible but wrong engineering advice.

Each file follows the same structure:

```
Description  → what the pattern does and why it matters for development
Usage        → when to reach for it and what to prepare
Prompt       → copy-paste template
Example      → full worked scenario with realistic code and context
Variations   → alternative pipelines and use cases (where applicable)
Tips         → constraints and gotchas from real usage
Related      → patterns that pair well together
```



## Combining patterns

Most engineering tasks benefit from stacking two or more patterns. Common combinations:


| Goal                                              | Combination                                                                                           |
| ------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| Expert reasoning with auditable trade-offs        | [Role Prompting](./role-prompting.md) + [Chain of Thought](./chain-of-thought.md)                     |
| Consistent code transformations in a fixed format | [Few-Shot](./few-shot.md) + [Structured Output](./structured-output.md)                               |
| End-to-end feature pipeline with typed handoffs   | [Prompt Chaining](./prompt-chaining.md) + [Structured Output](./structured-output.md) at every step   |
| Deep analysis feeding an implementation spec      | [Prompt Chaining](./prompt-chaining.md) + [Chain of Thought](./chain-of-thought.md) in analysis steps |
| Domain expertise with repeatable output style     | [Role Prompting](./role-prompting.md) + [Few-Shot](./few-shot.md)                                     |


When chaining prompts, pass structured output between steps. Prose handoffs reintroduce ambiguity typed JSON means you can inspect and correct at any stage before errors compound.

## Contributing

To add or improve a development prompt block, follow the structure in the existing files and keep examples grounded in realistic code and engineering context. See [../CONTRIBUTING.md](../CONTRIBUTING.md).