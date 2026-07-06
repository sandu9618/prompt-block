# Structured Output - Writing

## Description

Instructs the AI to return writing assets in a precise, structured format instead of a single block of prose. Rather than one long draft you manually dissect and repurpose, you get a typed JSON object with every content component in its own field, headline options, meta description, section summaries, social copy, and the draft body each ready to route to the right destination without further parsing.

In writing workflows, structured output closes the gap between content generation and content distribution. A Medium article draft that also returns its LinkedIn teaser, its Twitter hook, its SEO meta description, and three headline variants in the same response is not just a draft, it is a complete publishing package that feeds your scheduler, your CMS, and your analytics tagging in one prompt.

## Usage

Define the exact output schema upfront, every field the content needs to produce, its type, its character or word limit, and what platform or purpose it serves. Instruct the model explicitly to return only the JSON with no prose wrapper, no markdown fences, and no explanation.

Use this pattern for: multi-platform content packages, CMS-ready article
output, automated newsletter generation, social content calendars, SEO
content briefs, and any writing workflow where the same content needs to
be formatted differently for multiple destinations simultaneously.

## Prompt

```markdown
Write the following content and return it as a structured JSON object.

## Brief

[Describe the content — topic, audience, angle, key points to cover,
and tone.]

## Platform context

- **Primary format**: [e.g. Medium article, LinkedIn native post, newsletter]
- **Audience**: [Who will read this]
- **Publication**: [Where it appears]
- **Goal**: [What the content must accomplish]

## Output Schema

Return ONLY a valid JSON object matching this exact schema.
No explanation, no markdown fences, no text before or after the JSON.

\```typescript
{
  [field: string]: string          // [what this field is and where it goes]
  [field: string]: string[]        // [array of variants or options]
  [field: string]: {
    [platform: string]: string     // [platform-specific versions]
  }
}
\```

## Rules

- Return raw JSON only — no \`\`\`json fences, no preamble, no explanation
- Character limits are hard limits — do not exceed them
- [field] must be written in [tone constraint]
- Headlines must be specific enough to stand alone — no "How to X"
  without stating what X achieves or why it matters
- Body content must be complete and publication-ready — not a summary
  or placeholder
```



## Example Input

```markdown
Write a complete content package for a Medium technical article and return
it as a structured JSON object.

## Brief

Topic: Chain of Thought prompting — how adding "think step by step"
to an LLM prompt dramatically improves output quality on reasoning tasks.

Audience: Mid-level to senior software engineers who build AI features
and have experienced inconsistent LLM output on architectural or
debugging decisions.

Angle: The model isn't wrong — your prompt is incomplete. Chain of
Thought works because it forces the model to surface its reasoning
before committing to an answer, which makes errors visible and
correctable before they reach production.

Key points to cover:
1. The problem: LLMs pattern-match to confident answers, skipping reasoning
2. What Chain of Thought is and why it works mechanically
3. Before/after prompt example on an architectural decision
4. When to use it — and the one case where it backfires
5. How to combine it with Role Prompting for complex decisions

Tone: Peer-to-peer, direct, technically precise. Engineer writing for engineers.

## Platform context

- **Primary format**: Medium article (1,000–1,200 words)
- **Audience**: Software engineers with intermediate AI/LLM experience
- **Publication**: Personal Medium publication, cross-posted to Dev.to
- **Goal**: Build credibility as an AI practitioner, drive traffic to
  promptblocks GitHub repo, generate comments from practitioners

## Output Schema

Return ONLY a valid JSON object matching this exact schema.
No explanation, no markdown fences, no text before or after the JSON.

\```typescript
{
  headlines: {
    primary: string          // main headline — max 80 chars, specific and counterintuitive
    alternativeA: string     // second option — more question-driven
    alternativeB: string     // third option — more benefit-driven
  }
  seoMetaDescription: string // 140–155 chars, includes primary keyword, written for search intent
  mediumSubtitle: string     // 100–120 chars — the deck that appears under the headline on Medium
  tldr: string               // 2–3 sentences, written for someone who reads only this — complete thought
  articleBody: string        // full article, 1000–1200 words, markdown formatted with ## section headers
  linkedInTeaser: string     // 900–1200 chars — standalone LinkedIn post that teases the article,
                             // ends with a link prompt ("Full article in comments"), no hashtags
  twitterHook: string        // 240 chars max — the single sharpest sentence from the article,
                             // written to stand alone as a tweet
  devToTags: string[]        // 4 relevant Dev.to tags, lowercase, no spaces
  githubRepoMention: string  // 1 sentence naturally referencing promptblocks repo — not promotional
  canonicalUrl: string       // placeholder — "https://medium.com/@yourhandle/[slug]"
}
\```

## Rules

- Return raw JSON only — no \`\`\`json fences, no preamble, no explanation
- Character limits are hard limits — do not exceed them
- All headlines must be specific enough to stand alone — no generic
  "How to Use Chain of Thought Prompting"
- articleBody must be complete and publication-ready — not a summary
- linkedInTeaser must work as a standalone post — not a teaser that
  requires the article to make sense
- githubRepoMention must feel earned and contextual — not a CTA
```



## Example Output

```json
{
  "headlines": {
    "primary": "The Model Got Smarter. Your Prompt Got Worse.",
    "alternativeA": "Why Does a Better Model Give You a Worse Answer?",
    "alternativeB": "Add Four Words to Your Prompt. Get Reasoning Instead of Guessing."
  },
  "seoMetaDescription": "Chain of Thought prompting forces LLMs to reason before answering. Learn how to use it on architectural decisions, debugging, and complex engineering tasks.",
  "mediumSubtitle": "How adding 'think step by step' changes your LLM from a pattern-matcher into a reasoning partner — and when not to use it.",
  "tldr": "LLMs default to pattern-matching — producing confident answers that skip the reasoning. Chain of Thought prompting fixes this by instructing the model to work through a problem step by step before committing to an answer, making errors visible before they reach production. It takes four words to add and dramatically changes output quality on any task where the path to the answer matters.",
  "articleBody": "## The Confident Wrong Answer Problem\n\nYou asked your LLM whether to use a monolith or microservices. It said microservices — confidently, with five bullet points and a list of recommended technologies. You shipped the monolith anyway. Six months later, you were right.\n\nThe model wasn't lying. It was pattern-matching. Microservices appear in more positive contexts in its training data than monoliths do. It produced the statistically most common answer, dressed it up with structure, and delivered it with the confidence of a principal engineer.\n\nThis is the core failure mode of LLM reasoning tasks: the model skips the thinking and jumps to the conclusion. And because the output looks authoritative — formatted, detailed, technically fluent — you don't always notice until you've already made the wrong decision.\n\n## What Chain of Thought Actually Does\n\nChain of Thought prompting is the practice of explicitly instructing the model to reason through a problem before answering it. The canonical form is: 'Think through this step by step before giving your recommendation.'\n\nThat instruction changes what the model does internally. Instead of activating the most statistically common answer, it generates intermediate reasoning steps — working through the problem the way a human expert would, evaluating options, and only then committing to a conclusion.\n\nThe output changes in two ways. First, the answer is better calibrated — because the reasoning surfaced context the model would otherwise have skipped. Second, the reasoning is visible — which means you can read it, catch a flawed assumption, and correct it before acting on the conclusion.\n\n## Before and After\n\nHere is the same architectural question with and without Chain of Thought.\n\n**Without:**\n```\nShould we use a monolith or microservices?\nWe have 3 engineers and 200 customers.\n```\nOutput: A confident microservices recommendation with generic justification.\n\n**With Chain of Thought:**\n```\nThink through the following step by step before recommending.\nConsider team size, operational complexity, deployment risk, and\nscale requirements separately before concluding.\n\nShould we use a monolith or microservices?\nWe have 3 engineers, 200 customers, and deploy twice a week.\n```\nOutput: A structured evaluation that flags the operational overhead of microservices for a three-person team, notes that 200 customers does not justify distributed systems complexity, and recommends a modular monolith with clear service boundaries — with every step of the reasoning visible.\n\nThe answer changed because the reasoning changed. That is the point.\n\n## When to Use It\n\nChain of Thought produces the most significant quality improvement on tasks where the path to the answer matters as much as the answer itself.\n\n- Architectural decisions where multiple approaches are defensible\n- Debugging where the root cause is not obvious from the symptoms\n- Trade-off analysis where constraints need to be weighted against each other\n- Code review where the reviewer needs to consider security, performance, and readability simultaneously\n\nIt is less useful — and sometimes counterproductive — on tasks with a single correct answer that requires recall rather than reasoning. Asking the model to 'think step by step' about what the Node.js `fs.readFile` signature is adds latency without improving accuracy.\n\n## Combining It With Role Prompting\n\nChain of Thought and Role Prompting are the two patterns that compound most reliably. A role defines the expertise the model activates. Chain of Thought defines how it applies that expertise.\n\n```\nYou are a staff engineer at a high-traffic SaaS company.\nThink through the following step by step before recommending.\nConsider team size, operational complexity, and scale requirements separately.\n\nShould we use a monolith or microservices?\nWe have 3 engineers, 200 customers, and deploy twice a week.\n```\n\nThe role anchors the vocabulary and standards. The step-by-step instruction ensures the reasoning is explicit. Together they produce output that reads like a senior engineer walking through a decision — not a search engine returning the most popular answer.\n\n## The Practical Rule\n\nIf you are about to ask an LLM a question where you would want a human expert to explain their reasoning before giving an answer — add Chain of Thought. If the question has a lookup answer, skip it.\n\nThe pattern takes four words to add. The quality improvement on reasoning tasks is consistent enough that it belongs in your default prompt structure for any decision that matters.\n\nAll five patterns covered in this series — including Chain of Thought, Role Prompting, Few-Shot, Structured Output, and Prompt Chaining — are available as a TypeScript prompt library in the promptblocks repository, with working examples for each.",
  "linkedInTeaser": "I asked an LLM whether to use a monolith or microservices for a 3-person team with 200 customers.\n\nIt said microservices. Confidently. With bullet points.\n\nIt was wrong — and the reason it was wrong is interesting.\n\nLLMs default to pattern-matching. They produce the most statistically common answer in their training data, formatted to look authoritative. On factual recall tasks, that's fine. On reasoning tasks — architectural decisions, debugging, trade-off analysis — it's how you get confident wrong answers.\n\nChain of Thought prompting fixes this. You add four words to your prompt: 'Think through this step by step.'\n\nThat instruction changes what the model does. Instead of jumping to the conclusion, it generates intermediate reasoning steps — working through the problem explicitly before committing to an answer. The output is better calibrated. And because the reasoning is visible, you can catch a flawed assumption before acting on it.\n\nI wrote a full breakdown: the before/after prompt comparison, when it works and when it doesn't, and how to combine it with Role Prompting for complex decisions.\n\nFull article in comments.",
  "twitterHook": "LLMs don't give you wrong answers. They give you confident pattern-matches. Chain of Thought prompting is how you force reasoning instead.",
  "devToTags": ["promptengineering", "llm", "typescript", "ai"],
  "githubRepoMention": "The working TypeScript implementations of all five patterns in this series — including Chain of Thought — are in the promptblocks repository, structured so you can copy individual patterns into any project.",
  "canonicalUrl": "https://medium.com/@yourhandle/chain-of-thought-the-prompt-pattern-that-fixes-confident-wrong-answers"
}
```



## Variations


| Scenario                | Schema to define                                                                  |
| ----------------------- | --------------------------------------------------------------------------------- |
| Newsletter issue        | `{ subject, previewText, body, ctaText, ctaUrl, tweetableQuote }`                 |
| LinkedIn content series | `{ postBody, hook, hashtags[], carouselSlides[], engagementQuestion }`            |
| Landing page copy       | `{ headline, subheadline, heroBody, features[], cta, seoTitle, metaDescription }` |
| Case study              | `{ title, summary, challenge, solution, results[], pullQuote, socialSnippet }`    |
| Email sequence          | `{ subject, previewText, body, cta, replyPrompt, segmentTag }`                    |
| Developer docs page     | `{ title, description, quickstart, parameters[], examples[], relatedLinks[] }`    |
| YouTube video script    | `{ title, hook, chapters[], cta, description, tags[] }`                           |




## Tips

**One prompt, one publishing package.**
The highest-value use of structured output in writing is producing
every platform variant in a single response. A Medium article that also
returns its LinkedIn post, Twitter hook, and meta description in the
same JSON removes four separate prompts from your workflow every week.

**Character limits in the schema enforce platform constraints.**
`linkedInTeaser: string // 900–1200 chars` is not a suggestion, it is the platform's optimal range. Building limits into the schema means you never get a 3,000-word LinkedIn post or a 40-character meta description. The model treats schema comments as constraints if you phrase them that way.

`articleBody` **in markdown means CMS-ready output.**
If your `articleBody` field uses `##` headers and backtick code fences,
the JSON value pastes directly into Medium, Dev.to, or Ghost without
reformatting. Specify the markdown conventions you want in the rules.

**The** `twitterHook` **field reveals your best sentence.** Asking for a 240-character standalone tweet forces the model to identify the single sharpest idea in the entire piece. Even if you never post it, this field tells you what your article's most publishable insight is, and whether it's strong enough to anchor the whole piece.

**Validate body length before publishing.**
`articleBody` word counts are targets, not guarantees. Run a word count
check on the parsed value before sending to your CMS. If it's under target,
add a specific section to the brief and regenerate that field only.

## Related Prompts

- [role-prompting.md](./role-prompting.md) - assign a writer persona to shape voice before generating the structured package
- [chain-of-thought.md](./chain-of-thought.md) - plan the argument structure first, then generate the full package as structured output
- [prompt-chaining.md](./prompt-chaining.md) - use structured output as the final step in a plan → draft → package pipeline



## Tags

`structured-output` `json` `content-package` `multi-platform` `medium` `linkedin` `seo` `writing` `prompt-patterns`