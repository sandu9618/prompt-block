# Prompt Chaining - Writing

## Description

Breaks a complex content task into a sequence of focused writing steps where the output of each stage becomes the structured input of the next. Instead of asking the model to find the angle, plan the structure, write 1,200 words, and polish the prose all at once, you decompose the work into stages each one with a single editorial responsibility and a verifiable output.

In writing, chaining solves the core quality problem of single-prompt approaches: when you ask for a full article in one shot, the model picks the first plausible angle and writes toward it without testing whether the structure or voice is right. Chaining forces editorial decisions to happen before drafting begins. A weak angle surfaces at Step 1, not after you've published 1,500 words built on the wrong foundation.

## Usage

Map the natural stages of your content workflow where does the angle need to be locked before the outline can be written? Where does the outline need to be complete before drafting starts? Each stage boundary is a chain link.

Pass structured output between every step. Prose from Step 2 pasted into Step 3 reintroduces ambiguity. A typed JSON editorial plan or section-level outline means the next step receives reliable, inspectable data and you can correct a bad decision at any stage before it compounds into a full draft.

Use this pattern for: long-form articles, newsletter issues, case studies,
LinkedIn thought leadership, multi-platform content packages, content series
with consistent voice, and any writing task complex enough that a single
prompt produces a shallow angle, inconsistent structure, or generic voice.

## Prompt

```markdown
## Step [N] of [TOTAL] — [STEP NAME]

### What this step does
[One sentence describing the single editorial responsibility of this step.]

### Input from previous step
[Paste the output of the previous step here — or the raw brief
if this is Step 1.]

\```[format]
[Previous step output or initial brief]
\```

### Your task
[Focused instruction for this step only. Do not ask it to draft,
edit, or generate distribution copy if this is a planning step.]

### Output format
[Exact schema, structure, or format this step must return so the next
step can consume it without ambiguity.]

### Rules
- Focus only on this step — do not perform work that belongs to a later stage
- [Constraints specific to this step]
- Return [FORMAT] only — no explanation unless explicitly requested
```



## Example - Full 4-Step Chain

A pipeline that takes a raw content brief and produces a publication-ready Medium article with a distribution package broken into: plan → outline → draft → edit and package.

---



### Step 1 of 4 - Lock the Editorial Plan

```markdown
## Step 1 of 4 — Lock the Editorial Plan

### What this step does
Turn a raw topic brief into a locked editorial plan — argument, angle,
headline, and section-level belief arc — before any drafting begins.

### Input
\```
Topic: 5 Prompt Patterns Every Developer Should Know

Audience: Mid-level to senior software engineers who build AI features
and treat prompting as guesswork rather than engineering.

Publication: Personal Medium publication, cross-posted to Dev.to.

Goal: Teach five repeatable prompt patterns, reframe prompting as an
engineering discipline, and drive traffic to the promptblocks GitHub repo.

Constraints:
- 1,000–1,200 words
- Peer-to-peer tone — engineer writing for engineers
- Must not feel like AI hype or a product pitch
- Repo mention should feel earned, not promotional
\```

### Your task
Reason through the editorial decisions and produce a locked plan.
Identify the single defensible argument, the counterintuitive angle,
the recommended headline, and the section-by-section belief arc.
Do not write the article, outline sections in detail, or draft prose yet.

### Output format
Return a JSON object only — no explanation.

\```typescript
{
  coreArgument: string              // a claim the reader could agree or disagree with
  counterintuitiveAngle: string   // the surprising way into the topic
  headline: {
    primary: string
    alternativeA: string
    alternativeB: string
  }
  audienceBeliefToShift: string   // what the reader currently thinks vs what they need to believe
  sectionArc: {
    sectionTitle: string
    readerBeliefAfter: string     // what the reader must believe to continue
    keyPoint: string              // one sentence on what this section accomplishes
  }[]
  biggestRisk: string             // most likely way this piece fails editorially
  voiceNotes: string[]            // 3–5 specific tone constraints for the draft step
}
\```

### Rules
- coreArgument must be specific enough to disagree with — not a topic area
- sectionArc should have 5–7 sections including intro and conclusion
- Do not draft any article prose
- Return raw JSON only
```

**Step 1 Output:**

```json
{
  "coreArgument": "Prompting is an engineering discipline with repeatable patterns — not a temporary skill or a guessing game that better models will automate away.",
  "counterintuitiveAngle": "Most developers blame the model when output is inconsistent. The real problem is they are using one monolithic prompt where a chain of focused steps would produce reliable results every time.",
  "headline": {
    "primary": "5 Prompt Patterns Every Developer Should Know",
    "alternativeA": "Stop Tweaking Prompts. Start Engineering Them.",
    "alternativeB": "Your LLM Isn't Inconsistent — Your Prompt Architecture Is"
  },
  "audienceBeliefToShift": "Readers currently treat prompting as trial-and-error tweaking. They need to see it as problem decomposition for a non-deterministic system — the same skill that makes API design and test architecture reliable.",
  "sectionArc": [
    {
      "sectionTitle": "The Guessing Problem",
      "readerBeliefAfter": "My inconsistent LLM output is a prompt design problem, not a model problem.",
      "keyPoint": "Open with the familiar tweak-and-ship loop, then reframe it as engineering debt."
    },
    {
      "sectionTitle": "Pattern 1: Role Prompting",
      "readerBeliefAfter": "Assigning a persona changes reasoning style, not just vocabulary.",
      "keyPoint": "Before/after code review example showing the same function reviewed differently."
    },
    {
      "sectionTitle": "Pattern 2: Chain of Thought",
      "readerBeliefAfter": "Making the model show its work surfaces errors before they reach production.",
      "keyPoint": "Architectural decision example where confident-wrong becomes reasoned-right."
    },
    {
      "sectionTitle": "Pattern 3: Few-Shot Prompting",
      "readerBeliefAfter": "Examples teach voice and format better than paragraphs of instructions.",
      "keyPoint": "Commit message generation — two examples replace a page of style rules."
    },
    {
      "sectionTitle": "Pattern 4: Structured Output",
      "readerBeliefAfter": "Production AI features need typed output, not prose I parse manually.",
      "keyPoint": "TypeScript schema with the exact prompt instruction that enforces it."
    },
    {
      "sectionTitle": "Pattern 5: Prompt Chaining",
      "readerBeliefAfter": "Complex tasks need separation of concerns — one prompt per editorial stage.",
      "keyPoint": "Show how a plan → outline → draft chain beats one giant article prompt."
    },
    {
      "sectionTitle": "Prompting as Engineering",
      "readerBeliefAfter": "I have a repeatable toolkit, not a bag of tricks — and there is a library I can use today.",
      "keyPoint": "Close with the design patterns analogy and an earned link to promptblocks."
    }
  ],
  "biggestRisk": "The piece reads as a listicle of tips without the engineering reframe — readers finish knowing five names but not why patterns matter.",
  "voiceNotes": [
    "Open with a concrete observation, not a statistic or dictionary definition",
    "Use short punchy sentences to land key claims",
    "Never say 'In this article I will' or 'Let's dive in'",
    "Code examples must include a one-sentence setup before the block",
    "Repo mention only in the conclusion, framed as a practitioner resource"
  ]
}
```

---



### Step 2 of 4 - Build the Detailed Outline

```markdown
## Step 2 of 4 — Build the Detailed Outline

### What this step does
Expand the editorial plan into a section-by-section outline with word
budgets, specific examples to include, and transition notes.

### Input from Step 1
\```json
[paste Step 1 output here]
\```

### Your task
For each section in the sectionArc, produce a detailed outline entry
with a word budget, the specific example or code block to include,
and a transition sentence connecting it to the next section.
Total word budget across all sections must sum to 1,000–1,200 words.
Do not write full prose paragraphs yet.

### Output format
Return a JSON object only — no explanation.

\```typescript
{
  headline: string
  totalWordBudget: number
  sections: {
    order: number
    title: string
    wordBudget: number
    openingMove: string           // how this section opens — hook, callback, or transition
    keyPoints: string[]           // bullet-level content to cover
    exampleOrCode: string | null  // specific example, prompt, or code to include
    transitionToNext: string      // one sentence bridging to the following section
  }[]
  mustInclude: string[]           // elements that cannot be cut
  mustAvoid: string[]             // phrases, framings, or structures to exclude
}
\```

### Rules
- Word budgets must sum to 1,000–1,200
- Every section must reference a specific example — no abstract explanations alone
- Do not write draft prose
- Return raw JSON only
```

**Step 2 Output:**

```json
{
  "headline": "5 Prompt Patterns Every Developer Should Know",
  "totalWordBudget": 1150,
  "sections": [
    {
      "order": 1,
      "title": "The Guessing Problem",
      "wordBudget": 150,
      "openingMove": "Open with the tweak-and-ship loop every developer recognises",
      "keyPoints": [
        "You've written 'Summarise this document' — got mediocre output, tweaked, shipped",
        "That's not engineering — that's guessing",
        "Prompting has patterns the same way software has design patterns"
      ],
      "exampleOrCode": null,
      "transitionToNext": "The simplest pattern — and the one most developers skip — is giving the model a job title."
    },
    {
      "order": 2,
      "title": "Pattern 1: Role Prompting",
      "wordBudget": 180,
      "openingMove": "Before/after on the same code review task",
      "keyPoints": [
        "Without role: generic bug scan",
        "With role: security-focused fintech engineer review",
        "Same function, completely different output — role activates reasoning style"
      ],
      "exampleOrCode": "You are a senior TypeScript engineer at a fintech startup where security is non-negotiable. Review this function for bugs, with particular attention to SQL injection vectors.",
      "transitionToNext": "Role sets who the model is. Chain of Thought makes it show how it thinks."
    },
    {
      "order": 3,
      "title": "Pattern 2: Chain of Thought",
      "wordBudget": 200,
      "openingMove": "The monolith vs microservices decision — confident-wrong vs reasoned-right",
      "keyPoints": [
        "Without CoT: confident microservices recommendation for a 3-person team",
        "With CoT: surfaces operational overhead, recommends modular monolith",
        "The answer changed because the reasoning changed"
      ],
      "exampleOrCode": "Think through the following step by step before giving a recommendation. Consider team size, operational complexity, and scale requirements separately.",
      "transitionToNext": "CoT controls how the model reasons. Few-Shot controls how it writes."
    },
    {
      "order": 4,
      "title": "Pattern 3: Few-Shot Prompting",
      "wordBudget": 160,
      "openingMove": "Commit messages — two examples replace a paragraph of style rules",
      "keyPoints": [
        "Show 2 commit message examples in your preferred style",
        "Model infers format, tone, and detail level without explicit rules",
        "Works for voice transfer, not just format"
      ],
      "exampleOrCode": "Example 1: fix(auth): reject expired tokens before DB lookup\nExample 2: refactor(reports): extract aggregation into shared query builder",
      "transitionToNext": "Few-Shot shapes output style. Structured Output makes it machine-readable."
    },
    {
      "order": 5,
      "title": "Pattern 4: Structured Output",
      "wordBudget": 180,
      "openingMove": "Production AI features need JSON, not prose you parse with regex",
      "keyPoints": [
        "Define the schema upfront — every field, type, and constraint",
        "Instruct: return raw JSON only, no fences, no preamble",
        "Typed output feeds your app, your CMS, or the next step in a chain"
      ],
      "exampleOrCode": "typescript schema for { summary: string, severity: 'low' | 'medium' | 'high', actionItems: string[] }",
      "transitionToNext": "Structured Output handles one step. Chaining handles the whole pipeline."
    },
    {
      "order": 6,
      "title": "Pattern 5: Prompt Chaining",
      "wordBudget": 200,
      "openingMove": "One giant article prompt vs a plan → outline → draft chain",
      "keyPoints": [
        "Each step has one job — plan the angle, build the outline, write the draft",
        "Pass JSON between steps so errors surface early",
        "This article was planned with the same pattern it describes"
      ],
      "exampleOrCode": null,
      "transitionToNext": "Five patterns. One engineering mindset. Here is where to start."
    },
    {
      "order": 7,
      "title": "Prompting as Engineering",
      "wordBudget": 80,
      "openingMove": "Reframe from hope-it-works to I-know-which-pattern-to-reach-for",
      "keyPoints": [
        "Design patterns did not become obsolete when compilers improved",
        "Link to promptblocks as a TypeScript library implementing all five patterns",
        "Close without 'the possibilities are endless'"
      ],
      "exampleOrCode": null,
      "transitionToNext": null
    }
  ],
  "mustInclude": [
    "Design patterns analogy in the opening",
    "At least one before/after prompt comparison per pattern section",
    "Explicit statement that this article was built with prompt chaining"
  ],
  "mustAvoid": [
    "In this article I will",
    "Let's dive in",
    "ChatGPT",
    "The possibilities are endless",
    "Dictionary-definition opening"
  ]
}
```

---



### Step 3 of 4 - Write the First Draft

```markdown
## Step 3 of 4 — Write the First Draft

### What this step does
Write the complete first draft of the article from the detailed outline,
following word budgets and voice notes exactly.

### Input from Steps 1 and 2
\```json
[paste Step 1 output]
[paste Step 2 output]
\```

### Your task
Write the full article in markdown. Follow the section order, word budgets,
and examples specified in the outline. Apply the voiceNotes from Step 1.
This is a first draft — prioritise completing every section over polish.
Do not edit, cut for style, or generate distribution copy yet.

### Output format
Return a JSON object only — no explanation.

\```typescript
{
  headline: string
  articleBody: string             // full article in markdown with ## section headers
  wordCount: number
  sectionsWritten: number
  draftNotes: string[]            // anything you had to improvise or flag for the edit step
}
\```

### Rules
- articleBody must include all sections from the outline
- Respect word budgets — stay within ±10% per section
- Include every exampleOrCode from the outline verbatim or adapted naturally
- Return raw JSON only
```

**Step 3 Output (excerpt):**

```json
{
  "headline": "5 Prompt Patterns Every Developer Should Know",
  "articleBody": "## The Guessing Problem\n\nYou've probably written a prompt like this: \"Summarise this document.\" Got a mediocre response. Tweaked it. Got a slightly better one. Tweaked it again. Shipped something that works most of the time.\n\nThat's not engineering — that's guessing.\n\nPrompting has patterns. The same way Gang of Four design patterns gave us a shared vocabulary for solving recurring software problems, prompt patterns give you a repeatable toolkit for getting reliable, predictable output from any LLM...\n\n## Pattern 1: Role Prompting\n\nRole prompting is the simplest pattern and the one most developers skip...\n\n[... remaining sections through Pattern 5 and conclusion ...]",
  "wordCount": 1142,
  "sectionsWritten": 7,
  "draftNotes": [
    "Pattern 5 section ran 30 words over budget — may need trimming in edit",
    "CoT example needed slight expansion to make the before/after contrast clear"
  ]
}
```

---



### Step 4 of 4 - Edit and Package for Distribution

```markdown
## Step 4 of 4 — Edit and Package for Distribution

### What this step does
Edit the first draft for clarity, voice, and pacing — then produce a
complete distribution package with platform-specific variants.

### Input from Steps 1–3
\```json
[paste Step 1 output]
[paste Step 2 output]
[paste Step 3 output]
\```

### Your task
Edit the draft: tighten prose, fix transitions, cut anything in mustAvoid,
and resolve draftNotes. Then generate a full distribution package with
headline variants, SEO meta, and platform-specific teasers.
Every edit must preserve the coreArgument and sectionArc from Step 1.

### Output format
Return a JSON object only — no explanation.

\```typescript
{
  editedArticle: {
    headline: string
    body: string                  // publication-ready markdown
    wordCount: number
  }
  editsSummary: string[]          // what changed and why
  distributionPackage: {
    headlines: { primary: string; alternativeA: string; alternativeB: string }
    seoMetaDescription: string    // 140–155 chars
    mediumSubtitle: string        // 100–120 chars
    linkedInTeaser: string        // 900–1200 chars, standalone post
    twitterHook: string           // 240 chars max
    devToTags: string[]
  }
}
\```

### Rules
- editedArticle.body must be publication-ready — no placeholders
- linkedInTeaser must work as a standalone post without the article
- Resolve every item in draftNotes from Step 3
- Return raw JSON only
```

**Step 4 Output (excerpt):**

```json
{
  "editedArticle": {
    "headline": "5 Prompt Patterns Every Developer Should Know",
    "body": "[full publication-ready article — 1,128 words after tightening Pattern 5 section]",
    "wordCount": 1128
  },
  "editsSummary": [
    "Tightened Pattern 5 section by 14 words — cut redundant chain explanation",
    "Strengthened transition into CoT section — added explicit callback to role reasoning",
    "Moved repo link to final paragraph only — earned by the design patterns argument"
  ],
  "distributionPackage": {
    "headlines": {
      "primary": "5 Prompt Patterns Every Developer Should Know",
      "alternativeA": "Stop Tweaking Prompts. Start Engineering Them.",
      "alternativeB": "Your LLM Isn't Inconsistent — Your Prompt Architecture Is"
    },
    "seoMetaDescription": "Five prompt patterns — role prompting, chain of thought, few-shot, structured output, and chaining — that turn inconsistent LLM output into reliable engineering.",
    "mediumSubtitle": "Prompting isn't guesswork. It's an engineering discipline with repeatable patterns — and you already know how to think this way.",
    "linkedInTeaser": "You've probably done this: write a prompt, get mediocre output, tweak it, ship something that works most of the time.\n\nThat's not engineering. That's guessing.\n\nI use five prompt patterns across every AI feature I build. They work the same way design patterns work — a shared vocabulary for recurring problems...\n\nFull article in comments.",
    "twitterHook": "Prompting has patterns. The same way Gang of Four design patterns gave us a shared vocabulary for software problems, prompt patterns give you reliable LLM output.",
    "devToTags": ["ai", "promptengineering", "typescript", "llm"]
  }
}
```



## Variations


| Pipeline                    | Stages                                                                                    |
| --------------------------- | ----------------------------------------------------------------------------------------- |
| Newsletter issue            | Lock angle → Section outline → Write sections → Edit + subject line variants              |
| LinkedIn thought leadership | Audience belief audit → Argument arc → Draft post → Edit + comment prompts                |
| Case study                  | Extract customer data → Build narrative arc → Write draft → Edit + pull quotes for social |
| Technical tutorial          | Learning arc design → Code example plan → Write tutorial → Edit + SEO package             |
| Content series              | Series thesis → Issue brief → Draft in series voice → Edit + cross-link previous issues   |
| Landing page copy           | Value prop lock → Section wireframe → Write copy → Edit + A/B headline variants           |
| Ghostwriting                | Voice analysis → Outline in their style → Draft → Edit against voice samples              |




## Tips

**Four steps is the sweet spot for writing chains.**
Plan → Outline → Draft → Edit maps to how experienced writers actually work. Fewer steps compress editorial decisions that need separation. More steps create handoff overhead without meaningful quality gains.

**Step 1 should always be planning, not drafting.**
The most common chaining mistake is asking Step 1 to both find the angle and write the intro. Angle selection is a reasoning task. Drafting is an execution task. Mixing them produces output where the structure is already biased by prose committed too early.

**"Do not write the article yet" is the most important constraint.** Add this to every step that precedes your draft step. Without it, the model jumps ahead producing a shallow draft because it has already committed to the first plausible interpretation of your brief.

**Pass JSON between steps, not paragraphs.** An editorial plan as structured JSON means Step 3 receives the headline, argument arc, voice notes, and risk flags as typed fields not a paragraph it has to re-interpret. Use [structured-output.md](./structured-output.md) conventions at every intermediate step.

**Inspect each step before running the next.** The power of chaining is catching a weak angle at Step 1, a missing example at Step 2, or a voice drift at Step 3 before you've invested in a full distribution package. One corrected JSON object is cheaper than rewriting a published article.

**Chaining compounds the other four patterns.** Use [role-prompting.md](./role-prompting.md) to set the writer persona at Step 3. Use [chain-of-thought.md](./chain-of-thought.md) reasoning inside Step 1. Use [few-shot.md](./few-shot.md) at the draft step to lock in voice. Use [structured-output.md](./structured-output.md) at every handoff and the final distribution step.

## Related Prompts

- [role-prompting.md](./role-prompting.md) - assign a writer persona at the draft step to lock in voice
- [chain-of-thought.md](./chain-of-thought.md) - use CoT reasoning inside the editorial plan step for rigorous angle selection
- [structured-output.md](./structured-output.md) - enforce typed JSON at every intermediate step and the final distribution package
- [few-shot.md](./few-shot.md) - demonstrate your writing style at the draft step for consistent voice across a series



## Tags

`prompt-chaining` `content-pipeline` `editorial-workflow` `medium` `linkedin` `multi-platform` `writing` `prompt-patterns`