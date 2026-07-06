# Role Prompting - Writing

## Description

Assigns a professional writing persona to the AI before issuing a content task. By defining a specific writer role with voice, audience expertise, publication context, and stylistic constraints, you get output that reads like it came from a real writer with a point of view not a generic language model producing safe, averaged prose.

The same brief given to a "conversion copywriter" vs a "technical educator" vs a "narrative journalist" produces fundamentally different content. Role prompting lets you choose the voice before you hand over the brief and locks in tone, sentence structure, vocabulary level, and editorial standards before a single word is written.

## Usage

Define the writer's role with their specialisation, the publication or medium they write for, their audience, and their stylistic signature. Then give the writing task with clear instructions on format, length, and what the piece must accomplish not just what it must say.

Use this pattern for: blog posts, LinkedIn articles, technical tutorials,
email sequences, landing page copy, developer documentation, newsletter
issues, case studies, and any content task where voice and audience
alignment determine whether the piece works.

## Prompt

```markdown
You are a [SENIORITY] [WRITER ROLE] who writes for [PUBLICATION / MEDIUM /
AUDIENCE TYPE]. Your work appears in [WHERE IT GETS PUBLISHED — e.g. a
developer-focused newsletter, a B2B SaaS blog, a technical Medium publication].

Your writing style is [STYLE DESCRIPTORS — e.g. direct and opinionated /
conversational but precise / narrative-first with technical depth].
You write for readers who [DESCRIBE THE READER — their background,
what they already know, what they care about].

You never [ANTI-PATTERNS TO AVOID — e.g. use jargon without explanation /
open with a dictionary definition / write conclusions that restate the intro].

---

## Task

[Describe what you need written — format, length, goal, and call to action
if applicable.]

## Topic / Brief

[The subject matter, angle, key points to cover, and any specific examples
or data to include.]

## Constraints

- **Tone**: [e.g. confident but not arrogant / warm but not casual]
- **Length**: [word count or rough target]
- **Must include**: [specific points, examples, or sections required]
- **Must avoid**: [phrases, claims, or approaches to exclude]
- **CTA**: [what you want the reader to do or think after reading]
```



## Example Input

```markdown
You are a senior developer educator who writes for a technical audience
of mid-level to senior software engineers. Your work appears on a
developer-focused Medium publication with 40,000 subscribers.

Your writing style is conversational but precise — you explain complex
concepts through concrete analogies and working code, never through
abstract theory alone. You write for readers who understand TypeScript
and have built production APIs but may not have deep experience with
AI/LLM integration.

You never open with a dictionary definition, use phrases like "In today's
fast-paced world", write a conclusion that just restates the introduction,
or end with a generic "the possibilities are endless" closer.

---

## Task

Write a Medium article introduction and outline - not the full article yet.
The intro should hook the reader in the first three sentences, establish
your credibility angle, and set up the promise of the piece.
The outline should cover 5–6 sections with a one-sentence description
of what each section accomplishes.

Target length for the intro: 120–150 words.

## Topic / Brief

The article is titled: "5 Prompt Patterns Every Developer Should Know"

The angle: most developers treat prompting as guesswork — sending requests
to an LLM and hoping for the best. This article teaches them that prompting
is an engineering discipline with repeatable patterns, just like design
patterns in software architecture. The five patterns are: Role Prompting,
Chain of Thought, Few-Shot, Structured Output, and Prompt Chaining.

Key message: you don't need a PhD in machine learning to build reliable
AI features — you need the right patterns.

## Constraints

- **Tone**: confident, opinionated, peer-to-peer — engineer talking to engineers
- **Length**: 120–150 words for the intro; outline as concise section titles
  with one-sentence descriptions
- **Must include**: a concrete opening hook (not a statistic), the "design
  patterns" analogy for prompting, a clear promise of what the reader will
  be able to do after reading
- **Must avoid**: "In this article I will...", "Let's dive in", passive voice
  in the intro, any mention of ChatGPT
- **CTA**: reader should feel they're about to learn something that will
  immediately change how they build AI features
```



## Example Output

```markdown
## Introduction

You've probably written a prompt like this: "Summarise this document."
Got a mediocre response. Tweaked it. Got a slightly better one. Tweaked
it again. Shipped something that works most of the time.

That's not engineering — that's guessing.

Prompting has patterns. The same way Gang of Four design patterns gave us
a shared vocabulary for solving recurring software problems, prompt patterns
give you a repeatable toolkit for getting reliable, predictable output from
any LLM. You don't need to understand transformer architecture to use them.
You need to know five patterns, when to reach for each one, and how to
combine them.

By the end of this article, you'll have a mental model that changes how
you approach every AI feature you build — and a TypeScript library you
can use today.

---

## Article Outline

**1. Why Your Prompts Are Inconsistent (And It's Not the Model's Fault)**
Establishes that prompt inconsistency is an engineering problem, not a
model problem — and frames patterns as the solution, not prompt tweaking.

**2. Pattern 1: Role Prompting — Give the Model a Job Title**
Explains why assigning a persona activates a specific reasoning style,
with a before/after code review example showing the quality difference.

**3. Pattern 2: Chain of Thought — Make the Model Show Its Work**
Demonstrates how adding "think step by step" transforms answers on
architectural decisions from confident-but-wrong to reasoned-and-defensible.

**4. Pattern 3: Few-Shot Prompting — Teach by Example, Not Instruction**
Shows how 2–3 examples inside the prompt replace a paragraph of
instructions, using commit message generation as the concrete example.

**5. Pattern 4: Structured Output — Get JSON, Not Prose**
Covers why structured output is essential for production AI features,
with a TypeScript schema and the exact prompt instruction that enforces it.

**6. Pattern 5: Prompt Chaining — Break Complex Tasks Into Reliable Steps**
Introduces chaining as the "separation of concerns" of prompting — and
shows how a 3-step chain produces more reliable results than one giant prompt.

**Conclusion: Prompting as Engineering Discipline**
Reframes the reader's relationship with LLMs — from "hope it works" to
"I know which pattern to reach for" — and links to the promptblocks
GitHub repo for the full TypeScript library.
```



## Variations


| Scenario                    | Role to Use                                                                           |
| --------------------------- | ------------------------------------------------------------------------------------- |
| Developer tutorial          | `senior developer educator writing for mid-level engineers`                           |
| LinkedIn thought leadership | `principal engineer sharing hard-won lessons with their professional network`         |
| SaaS marketing blog         | `B2B content strategist writing conversion-focused posts for technical buyers`        |
| Newsletter issue            | `independent technical writer publishing a weekly newsletter for engineering leaders` |
| Landing page copy           | `direct response copywriter specialising in developer tools and SaaS`                 |
| Case study                  | `B2B content writer who turns customer success data into narrative proof`             |
| Technical documentation     | `developer experience writer who makes complex APIs feel approachable`                |
| Twitter/X thread            | `developer advocate who explains technical concepts in punchy, shareable threads`     |




## Tips

**"You never..." is as important as the role definition.**
Anti-patterns shape the output as much as the persona does. Telling the
model to never open with a dictionary definition, never use "In today's
fast-paced world", and never end with "the possibilities are endless"
eliminates the most common AI writing clichés before they appear.

**Describe the reader, not just the role.**
`"You write for mid-level engineers who understand TypeScript but haven't built AI features"` tells the model exactly what to assume and what to explain. Without reader context, the model calibrates to an imagined average which is usually wrong for your specific audience.

**Separate the "what" from the "how".**
`"Must include: the design patterns analogy"` tells the model what to
cover. `"Tone: peer-to-peer, engineer talking to engineers"` tells it
how to deliver it. Both are required. Content without tone produces
the right information in the wrong voice.

**Ask for the intro and outline before the full article.** For longer pieces, use this pattern as Step 1 of a chain get the structure and voice locked in before committing to 1,500 words. It is far easier to correct a 200-word intro than to rewrite a full article because the angle was wrong.

**Publish context changes vocabulary.** The same concept written for a Medium technical publication uses different vocabulary than the same concept written for a company blog read by non-technical executives. Specify where the piece will be published it calibrates word choice, assumed knowledge level, and sentence complexity.

## Related Prompts

- [chain-of-thought.md](./chain-of-thought.md) - use CoT to plan the argument structure before writing
- [few-shot.md](./few-shot.md) - show examples of your preferred writing style to lock in voice
- [prompt-chaining.md](./prompt-chaining.md) - use role prompting as Step 1 of a brief → outline → draft → edit pipeline



## Tags

`role-prompting` `content-writing` `developer-content` `technical-writing` `medium` `linkedin` `writing` `prompt-patterns`