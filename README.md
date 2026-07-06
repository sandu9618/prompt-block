# Prompt Blocks

A library of copy-paste prompt patterns for real work not abstract prompting theory. Each block is a complete template with usage guidance, a worked example, and tips for avoiding plausible-but-wrong output.

Organised by **domain** (what you're trying to accomplish) and **technique** (how the model should reason). Pick a category, pick a pattern, replace the placeholders, and run it.

## Categories


| Category                          | Audience                                      | Examples                                                              |
| --------------------------------- | --------------------------------------------- | --------------------------------------------------------------------- |
| **[Analysis](./analysis/)**       | Analysts, PMs, data scientists, operators     | Metric drops, churn diagnosis, research synthesis, competitive intel  |
| **[Development](./development/)** | Developers, tech leads, platform engineers    | Code review, debugging, architecture decisions, refactoring pipelines |
| **[Writing](./writing/)**         | Writers, editors, content marketers, founders | Angle finding, voice consistency, multi-platform publishing           |


Each category has its own README with a pattern index, quick-start guide, and common combinations:

- [Analysis prompt blocks](./analysis/README.md)
- [Development prompt blocks](./development/README.md)
- [Writing prompt blocks](./writing/README.md)

## Core patterns

Every category includes the same five techniques, adapted for that domain's vocabulary and failure modes:


| Pattern                                              | What it does                                                 |
| ---------------------------------------------------- | ------------------------------------------------------------ |
| [Chain of Thought](./analysis/chain-of-thought.md)   | Step-by-step reasoning before committing to a conclusion     |
| [Few-Shot](./analysis/few-shot.md)                   | Examples that lock in output format and quality              |
| [Prompt Chaining](./analysis/prompt-chaining.md)     | Multi-stage pipelines with verifiable handoffs between steps |
| [Role Prompting](./analysis/role-prompting.md)       | Domain expertise and perspective through a defined persona   |
| [Structured Output](./analysis/structured-output.md) | Machine-readable JSON for dashboards, CI, and automation     |


Links above point to the analysis versions — the same filenames exist in [development](./development/) and [writing](./writing/) with domain-specific prompts and examples.

## Quick start

1. **Pick a category** that matches your task  [analysis](./analysis/), [development](./development/), or [writing](./writing/).
2. **Pick a pattern** using the quick guide in that category's README.
3. **Open the file** and copy the template from the `## Prompt` section.
4. **Replace `[bracketed placeholders]`** with your data, code, or brief.
5. **Check the example and tips** before your first run, they set the quality bar and cover common mistakes.

Not sure where to start?

- Something broke and you need to know why → **Chain of Thought**
- You run this task weekly and want the same format → **Few-Shot**
- The work has stages that shouldn't be compressed → **Prompt Chaining**
- The same input needs a different expert lens → **Role Prompting**
- Output feeds code or a dashboard, not a human reader → **Structured Output**

## What's in each file

Every prompt block follows the same structure:

```
Description     → what the pattern does and why it matters in this domain
Usage           → when to reach for it and what to prepare
Prompt          → copy-paste template with [placeholders]
Example Input   → fully filled-in prompt with realistic context
Example Output  → what good model output looks like
Variations      → how to adapt for common scenarios
Tips            → constraints and gotchas from real usage
Related Prompts → patterns that pair well together
Tags            → keywords for discovery
```

## Combining patterns

Most tasks benefit from stacking two or more patterns. A few combinations that show up often:


| Goal                                         | Combination                                          |
| -------------------------------------------- | ---------------------------------------------------- |
| Expert reasoning you can audit               | Role Prompting + Chain of Thought                    |
| Repeatable output in a fixed shape           | Few-Shot + Structured Output                         |
| End-to-end pipeline with typed handoffs      | Prompt Chaining + Structured Output at every step    |
| Deep analysis feeding a concrete deliverable | Prompt Chaining + Chain of Thought in analysis steps |


When chaining prompts, pass structured output between steps. Prose handoffs reintroduce ambiguity — typed JSON lets you inspect and correct at any stage before errors compound.

## Repository layout

```
prompt-blocks/
├── analysis/          # Metrics, research synthesis, competitive intel, reporting
├── development/       # Code review, debugging, architecture, engineering automation
├── writing/           # Editorial planning, voice, multi-platform content
├── templates/         # Scaffolding for new prompt blocks
└── CONTRIBUTING.md    # How to add or improve blocks
```

## Contributing

Contributions should be practical, specific, and grounded in realistic scenarios — prompts someone can run in production, not generic advice.

See [CONTRIBUTING.md](./CONTRIBUTING.md) for file structure, quality standards, and the pull request process.

## License

[MIT](./LICENSE)