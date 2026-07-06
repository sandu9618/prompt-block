# Contributing to Prompt Blocks

Thank you for helping improve this collection. Prompt Blocks is a library of copy-paste prompt patterns organised by domain and technique. Each file is meant to be used directly in production workflows, not as abstract advice, so contributions should be practical, specific, and grounded in realistic scenarios.

## What belongs here

Contributions should help someone get **reliable output from an LLM on a real task**, not explain prompting theory in the abstract.

Good additions:

- A new domain-specific adaptation of an existing pattern (e.g. security review via role prompting in `development/`)
- A sharper example that shows what good output looks like for a common failure mode
- Tips that address mistakes you've actually hit in practice
- A new category if it serves a distinct audience with its own vocabulary and constraints

Out of scope:

- Generic "how to use ChatGPT" content with no copy-paste prompt
- Examples with placeholder data ("Company X saw a 20% drop…") instead of realistic specifics
- Prompts that only work for one proprietary tool unless the pattern generalises
- Duplicating an existing block with minor wording changes



## Repository structure

```
prompt-blocks/
├── analysis/          # Metrics, research synthesis, competitive intel, reporting
├── development/       # Code review, debugging, architecture, engineering automation
├── writing/           # Editorial planning, voice, multi-platform content
├── templates/         # Shared scaffolding for new prompt blocks
└── CONTRIBUTING.md
```

Each category contains the same five core patterns:


| Pattern           | File                   |
| ----------------- | ---------------------- |
| Chain of Thought  | `chain-of-thought.md`  |
| Few-Shot          | `few-shot.md`          |
| Prompt Chaining   | `prompt-chaining.md`   |
| Role Prompting    | `role-prompting.md`    |
| Structured Output | `structured-output.md` |


Each category also has a `README.md` that indexes the patterns, explains when to use them, and shows common combinations. **Update the category README** when you add a file or materially change what a pattern is best for.

## Prompt block file structure

Every prompt block file must follow this section order. Use an existing file in the same category as your reference — for example, `[analysis/chain-of-thought.md](./analysis/chain-of-thought.md)`.

```markdown
# {Pattern Name} — {Category}

## Description
## Usage
## Prompt
## Example Input
## Example Output
## Variations
## Tips
## Related Prompts
## Tags
```



### Section guidelines

**Description** — Two to four short paragraphs. Explain what the pattern does and why it matters *in this domain*. Focus on the failure mode it prevents, not a textbook definition.

**Usage** — When to reach for this pattern, what the reader should prepare before running the prompt, and what kinds of tasks it fits.

**Prompt** — A complete, copy-paste template inside a fenced `markdown` code block. Use `[bracketed placeholders]` for anything the user must supply. The prompt should work without editing anything outside the placeholders.

**Example Input** — A fully filled-in version of the prompt with realistic domain context. No lorem ipsum, no "insert your data here."

**Example Output** — What good model output looks like for that input. Show the structure and depth you expect — this is the quality bar for the block.

**Variations** — A table of common scenarios and how to adapt the prompt for each. Skip only if the pattern truly has no meaningful variants.

**Tips** — Practical constraints and gotchas from real usage. Lead with the instruction or mistake that matters most. Explain *why* it matters, not just what to do.

**Related Prompts** — Links to other files in the same category (or across categories) that pair well. One line per link explaining the combination.

**Tags** — Backtick-wrapped keywords on one line. Always include the pattern name, the category (`analysis`, `development`, or `writing`), and `prompt-patterns`.

## Content quality standards



### Examples must be realistic

Analysis examples need believable metrics, time ranges, and baselines. Development examples need real code with real trade-offs. Writing examples need concrete briefs, audiences, and publication context.

The example should be detailed enough that a reader can see *why* the pattern produced a better answer than a naive one-shot prompt.

### Prompts must be self-contained

Someone should be able to copy the `## Prompt` section, replace placeholders, and run it without reading the rest of the file. Cross-references, reasoning-stage numbering, and output format requirements belong inside the prompt itself.

### Tips must be earned

Prefer tips that prevent plausible-but-wrong output:

- "Do not jump to a conclusion" before listing hypotheses
- "Pass JSON between chain steps, not prose summaries"
- "Show 2–3 examples that cover different structural units, not three versions of the same paragraph"

Avoid tips that restate the obvious ("be specific", "provide context") unless you explain the specific failure they prevent.

### Voice and formatting

Match the tone of existing files: direct, practitioner-focused, no hype. Write in second person where it helps ("you") but keep sentences tight.

For code and prompt content inside fenced blocks, use consistent indentation and realistic naming. For JSON examples in structured-output blocks, use valid JSON.

## Adding a new prompt block

1. **Pick the right category** - `analysis`, `development`, or `writing`. If your audience and vocabulary don't fit any of these, open an issue first to discuss a new top-level folder.
2. **Pick the right pattern** - Use an existing pattern name unless you're proposing something genuinely new. New patterns need an issue for discussion before implementation.
3. **Create the file** - `{category}/{pattern}.md` using kebab-case, matching existing filenames.
4. **Follow the section structure** above. Start from `[templates/prompt-template.md](./templates/prompt-template.md)` or copy the closest existing file.
5. **Update the category README** - Add or revise the row in the patterns table and the quick-guide bullets if needed.
6. **Cross-link** - Add Related Prompts entries in peer files when the new block pairs naturally with existing ones.



## Improving an existing block

Small fixes (typos, clearer wording, one additional tip) are always welcome.

For larger changes:

- **Don't shrink examples** to save space. Examples are the most valuable part of each file.
- **Preserve the section order** even if you're only editing one section.
- **Note breaking changes** in your PR if you rename placeholders or change expected output shape — downstream users may have saved copies of the old template.



## Pull request process

1. Fork the repository and create a branch from `main`.
2. Make your changes following the guidelines above.
3. Open a pull request with:
  - **What** you added or changed
  - **Why** - the failure mode or gap it addresses
  - **How you tested it**- which model you ran the prompt against and whether the example output is representative
4. Link any related issue if one exists.

There is no CI or automated test suite. Reviewers will evaluate contributions against the quality standards in this document. PRs that follow the file structure and include strong examples are typically merged quickly.

## Review checklist

Before submitting, confirm:

- [ ] File follows the required section order
- [ ] `## Prompt` is copy-paste ready with clear `[placeholders]`
- [ ] Example Input is fully filled in with realistic domain content
- [ ] Example Output matches the quality and structure the prompt asks for
- [ ] Tips address non-obvious failure modes, not generic advice
- [ ] Related Prompts link to real files with a one-line rationale
- [ ] Tags include pattern name, category, and `prompt-patterns`
- [ ] Category README updated if patterns table or quick guide changed
- [ ] No secrets, API keys, or identifiable private data in examples



## Questions and larger changes

Open an issue before starting work if you plan to:

- Add a new top-level category
- Introduce a new pattern type beyond the five existing ones
- Restructure the repository layout
- Remove or merge existing blocks

For everything else — better examples, new tips, clearer prompts — a pull request is the fastest path.