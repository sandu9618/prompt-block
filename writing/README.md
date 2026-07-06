# Writing Prompt Blocks

Ready-to-use prompt patterns for content creation finding the right angle, maintaining voice, planning long-form pieces, and producing multi-platform publishing packages. Each block includes a description, usage guidance, a copy-paste prompt template, worked examples, and tips for getting reliable output.

These prompts are written for writers, content marketers, editors, and founders who need editorial quality from AI output, not generic prose that sounds like everyone else.

## When to use these

Reach for a writing prompt block when:

- A single prompt produces shallow angles or forgettable first drafts
- You need editorial decisions surfaced before drafting begins
- Voice consistency matters more than getting words on the page fast
- Output must feed a CMS, scheduler, or multi-platform distribution workflow
- A complex piece needs to be broken into verifiable editorial stages

For analytical work metrics, churn, competitive research see [../analysis/](../analysis/). For code and engineering tasks, see [../development/](../development/).

## Available patterns


| Pattern               | Best for                                                                                 | File                                           |
| --------------------- | ---------------------------------------------------------------------------------------- | ---------------------------------------------- |
| **Chain of Thought**  | Finding the right angle, argument structure, headline stress-testing, ambiguous briefs   | [chain-of-thought.md](./chain-of-thought.md)   |
| **Few-Shot**          | Voice consistency, content series, matching publication tone, sounding like you wrote it | [few-shot.md](./few-shot.md)                   |
| **Prompt Chaining**   | Multi-stage pipelines: angle → outline → draft → polish → distribution copy              | [prompt-chaining.md](./prompt-chaining.md)     |
| **Role Prompting**    | Choosing the writer persona: copywriter, educator, journalist, technical author          | [role-prompting.md](./role-prompting.md)       |
| **Structured Output** | CMS-ready drafts, multi-platform packages, social copy, SEO metadata in one response     | [structured-output.md](./structured-output.md) |




### Quick guide to picking a pattern

- **The angle is harder than the execution** → start with [Chain of Thought](./chain-of-thought.md)
- **It doesn't sound like your voice** → start with [Few-Shot](./few-shot.md)
- **The task has distinct editorial stages that shouldn't be compressed** → start with [Prompt Chaining](./prompt-chaining.md)
- **Voice and audience expertise should shape the piece** → start with [Role Prompting](./role-prompting.md)
- **The output needs to feed multiple platforms or tools, not just a doc** → start with [Structured Output](./structured-output.md)



## How to use a prompt block

1. **Open the pattern file** that matches your task.
2. **Copy the prompt template** from the `## Prompt` section.
3. **Replace the bracketed placeholders**  topic, audience, brief, constraints with your specifics.
4. **Read the example** to see what good output looks like for that pattern.
5. **Check Tips** before your first run, they cover the mistakes that produce plausible but flat content.

Each file follows the same structure:

```
Description  → what the pattern does and why it matters for writing
Usage        → when to reach for it and what to prepare
Prompt       → copy-paste template
Example      → full worked scenario with realistic briefs and output
Variations   → alternative pipelines and use cases (where applicable)
Tips         → constraints and gotchas from real usage
Related      → patterns that pair well together
```



## Combining patterns

Most writing tasks benefit from stacking two or more patterns. Common combinations:


| Goal                                               | Combination                                                                                           |
| -------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| Expert voice with auditable editorial reasoning    | [Role Prompting](./role-prompting.md) + [Chain of Thought](./chain-of-thought.md)                     |
| Consistent voice across a series in a fixed format | [Few-Shot](./few-shot.md) + [Structured Output](./structured-output.md)                               |
| End-to-end content pipeline with typed handoffs    | [Prompt Chaining](./prompt-chaining.md) + [Structured Output](./structured-output.md) at every step   |
| Deep angle work feeding a full draft               | [Prompt Chaining](./prompt-chaining.md) + [Chain of Thought](./chain-of-thought.md) in planning steps |
| Domain expertise with a repeatable voice           | [Role Prompting](./role-prompting.md) + [Few-Shot](./few-shot.md)                                     |


When chaining prompts, pass structured output between steps. Prose handoffs reintroduce ambiguity — typed JSON means you can inspect and correct at any stage before errors compound into a full draft.

## Contributing

To add or improve a writing prompt block, follow the structure in the existing files and keep examples grounded in realistic briefs and publication contexts. See [../CONTRIBUTING.md](../CONTRIBUTING.md).