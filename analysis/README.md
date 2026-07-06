# Analysis Prompt Blocks

Ready-to-use prompt patterns for analytical work metric investigation, user research synthesis, competitive intelligence, churn diagnosis, and recurring reporting. Each block includes a description, usage guidance, a copy-paste prompt template, worked examples, and tips for getting reliable output.

These prompts are written for analysts, product managers, data scientists, and operators who need defensible insights from messy data not generic summaries.

## When to use these

Reach for an analysis prompt block when:

- A single prompt produces shallow or inconsistent findings
- You need reasoning you can audit before acting on it
- Output must feed a dashboard, alert, or downstream workflow
- You run the same analysis weekly and want format consistency
- A complex investigation needs to be broken into verifiable stages

For content creation prompts, see [../writing/](../writing/). For code and engineering tasks, see [../development/](../development/).

## Available patterns


| Pattern               | Best for                                                                      | File                                           |
| --------------------- | ----------------------------------------------------------------------------- | ---------------------------------------------- |
| **Chain of Thought**  | Root cause analysis, anomaly investigation, hypothesis testing                | [chain-of-thought.md](./chain-of-thought.md)   |
| **Few-Shot**          | Recurring reports, feedback tagging, survey coding, consistent finding format | [few-shot.md](./few-shot.md)                   |
| **Prompt Chaining**   | Multi-stage pipelines — collect → diagnose → prioritise → prescribe           | [prompt-chaining.md](./prompt-chaining.md)     |
| **Role Prompting**    | Choosing the analytical lens — product, security, data science, competitive   | [role-prompting.md](./role-prompting.md)       |
| **Structured Output** | Machine-readable JSON for dashboards, alerts, and automated pipelines         | [structured-output.md](./structured-output.md) |




### Quick guide to picking a pattern

- **Something broke and you need to know why** → start with [Chain of Thought](./chain-of-thought.md)
- **You run this analysis every week and want the same format** → start with [Few-Shot](./few-shot.md)
- **The task has distinct stages that shouldn't be compressed** → start with [Prompt Chaining](./prompt-chaining.md)
- **The same data needs a different perspective** → start with [Role Prompting](./role-prompting.md)
- **The output needs to be consumed by code, not read by a human** → start with [Structured Output](./structured-output.md)



## How to use a prompt block

1. **Open the pattern file** that matches your task.
2. **Copy the prompt template** from the `## Prompt` section.
3. **Replace the bracketed placeholders** — problem description, data, schema, constraints — with your specifics.
4. **Read the example** to see what good output looks like for that pattern.
5. **Check Tips** before your first run — they cover the mistakes that produce plausible but wrong analysis.

Each file follows the same structure:

```
Description  → what the pattern does and why it matters for analysis
Usage        → when to reach for it and what to prepare
Prompt       → copy-paste template
Example      → full worked scenario with realistic data
Variations   → alternative pipelines and use cases (where applicable)
Tips         → constraints and gotchas from real usage
Related      → patterns that pair well together
```



## Combining patterns

Most analysis tasks benefit from stacking two or more patterns. Common combinations:


| Goal                                             | Combination                                                                                            |
| ------------------------------------------------ | ------------------------------------------------------------------------------------------------------ |
| Rigorous investigation with auditable reasoning  | [Role Prompting](./role-prompting.md) + [Chain of Thought](./chain-of-thought.md)                      |
| Consistent weekly reports in a fixed format      | [Few-Shot](./few-shot.md) + [Structured Output](./structured-output.md)                                |
| End-to-end analysis pipeline with typed handoffs | [Prompt Chaining](./prompt-chaining.md) + [Structured Output](./structured-output.md) at every step    |
| Deep diagnosis feeding an automated playbook     | [Prompt Chaining](./prompt-chaining.md) + [Chain of Thought](./chain-of-thought.md) in diagnosis steps |
| Domain expertise with repeatable output style    | [Role Prompting](./role-prompting.md) + [Few-Shot](./few-shot.md)                                      |


When chaining prompts, pass structured output between steps. Prose handoffs reintroduce ambiguity — typed JSON means you can inspect and correct at any stage before errors compound.

## Contributing

To add or improve an analysis prompt block, follow the structure in the existing files and keep examples grounded in realistic business data. See [../CONTRIBUTING.md](../CONTRIBUTING.md).