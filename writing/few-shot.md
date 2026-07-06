# Few-Shot Prompting - Writing

## Description

Teaches the AI your exact writing voice by showing it concrete examples of your style before handing over the real task. Instead of describing your voice in abstract terms "conversational but precise", "opinionated but not arrogant" you demonstrate it. The model writes in your voice, not its own averaged default.

In writing, voice is the hardest thing to specify and the most important thing to get right. Two writers covering the same topic with the same facts produce completely different content based on sentence rhythm, vocabulary choice, how they handle transitions, and how confidently they state claims. Few-shot prompting transfers all of that implicitly the model infers your style rules from the examples without you needing to articulate them.

## Usage

Provide 2–3 examples of your own writing or writing in the exact style you want that cover the structural units relevant to your task: an opening hook, a section transition, a way of introducing code, a closing line. Point out what to notice. Then give the new brief at the end.

Use this pattern for: maintaining a consistent voice across a content
series, writing in someone else's established style, matching the tone
of an existing publication, generating content that sounds like you wrote
it, and any writing task where "it doesn't sound like me" is the most
common failure mode.

## Prompt

```markdown
I'm going to show you [NUMBER] examples of my writing style.
Study them carefully — pay attention to [WHAT TO NOTICE: sentence length /
how I open sections / how I handle technical concepts / confidence level
of claims / how transitions work / how I close pieces].

Do not summarise or describe my style — absorb it and write in it.

---

## Examples

### Example 1 — [What this example demonstrates]
\```
[Paste your actual writing here — a paragraph, an intro, a section,
whatever structural unit is most relevant to the task.]
\```

---

### Example 2 — [What this example demonstrates]
\```
[Second writing sample — ideally from a different piece to show
the style is consistent, not topic-specific.]
\```

---

### Example 3 — [What this example demonstrates — optional but recommended]
\```
[Third sample — use this to show how you handle a specific challenge:
a complex concept, a transition, a closing line, or a code introduction.]
\```

---

## Now write in this style

**Task:** [What you want written — format, length, topic.]

**Brief:**
[The subject matter, key points, angle, and any structural requirements.]

**Constraints:**
- Length: [target word count or range]
- Must include: [specific points or elements]
- Must avoid: [phrases, structures, or approaches to exclude]
```



## Example Input

```markdown
I'm going to show you 3 examples of my writing style.
Study them carefully — pay attention to how I open with a concrete
observation before stating the broader point, how I use short punchy
sentences to land a key claim, how I introduce code with a single
sentence that explains what the reader is about to see, and how I
close sections by connecting the technical point back to the practical
consequence.

Do not summarise or describe my style — absorb it and write in it.

---

## Examples

### Example 1 — Opening hook and first section
\```
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
\```

---

### Example 2 — Introducing a technical concept
\```
Role prompting is the simplest pattern and the one most developers
skip. Here's what it looks like without it:

"Review this function for bugs."

Here's what it looks like with it:

"You are a senior TypeScript engineer at a fintech startup where
security is non-negotiable. Review this function for bugs, with
particular attention to SQL injection vectors and missing error handling."

Same function. Completely different review. The second prompt doesn't
just get more feedback — it gets the right kind of feedback, calibrated
to the context that actually matters.
\```

---

### Example 3 — Closing a section and transitioning
\```
This is why role prompting isn't a trick — it's a contract. You're
telling the model what expertise to activate, what standards to apply,
and what to prioritise when it has to make a judgement call. The output
quality ceiling is set before you ask the question.

Once you've assigned a role, you're ready for the second pattern —
one that solves the problem you've definitely hit already: the model
that jumps to a confident answer without showing its work.
\```

---

## Now write in this style

**Task:** Write the introduction and first full section of a Medium article.
Target length: 250–300 words total.

**Brief:**
Article title: "Chain of Thought: The Prompt Pattern That Fixes Confident
Wrong Answers"

The intro should open with a concrete scenario a developer will recognise —
getting a confident but wrong answer from an LLM on an architectural decision.
The first section should explain what Chain of Thought prompting is,
why it works, and show a before/after example using an architectural decision
(monolith vs microservices for a small team).

**Constraints:**
- Length: 250–300 words
- Must include: a before/after prompt comparison showing CoT in action
- Must avoid: "Let's dive in", "In this article", passive voice,
  any definition that opens with "Chain of Thought is a technique where..."
- The before/after should show the same question with and without the
  step-by-step instruction — same question, different prompt structure
```



## Example Output

```markdown
You asked the model whether to use a monolith or microservices for your
new SaaS. It told you microservices — confidently, with five bullet
points, a diagram description, and a list of recommended technologies.

You shipped the monolith anyway. Six months later, you were right.

The model wasn't lying. It was pattern-matching to the most common answer
in its training data: microservices are modern, monoliths are legacy, case
closed. It never considered your team size, your deployment cadence, your
infrastructure budget, or the fact that three engineers do not need a
service mesh.

---

## The Pattern That Makes the Model Show Its Work

The problem wasn't the model. It was the prompt.

"Should we use a monolith or microservices?" is an invitation to guess.
"Think through this step by step before giving a recommendation" is an
invitation to reason.

Here's the difference:

**Without Chain of Thought:**
\```
Should we use a monolith or microservices for our B2B SaaS?
We have 3 engineers and 200 customers.
\```
Output: A confident recommendation for microservices with generic justification.

**With Chain of Thought:**
\```
Think through the following step by step before giving a recommendation.
Consider team size, operational complexity, deployment risk, and
scale requirements separately before concluding.

Should we use a monolith or microservices for our B2B SaaS?
We have 3 engineers, 200 customers, and deploy twice a week.
\```
Output: A structured evaluation that surfaces the operational overhead
of microservices for a three-person team, flags that 200 customers does
not justify distributed systems complexity, and recommends a modular
monolith with clear service boundaries — with the reasoning visible
and auditable.

The answer changed because the reasoning changed. That's the point.
```



## Variations


| Scenario                | What to use as examples                                          |
| ----------------------- | ---------------------------------------------------------------- |
| Article series          | 2–3 intros from previous articles in the series                  |
| Ghostwriting            | 2–3 samples of the person's existing published content           |
| Brand voice matching    | 2–3 paragraphs from the brand's best-performing content          |
| Newsletter issues       | 2–3 opening paragraphs from previous issues                      |
| LinkedIn posts          | 2–3 of your best-performing posts                                |
| Technical documentation | 2–3 sections from existing docs that match the tone you want     |
| Email sequences         | 2–3 emails from the sequence with the style you want to maintain |




## Tips

**Paste real writing not writing you want to emulate.** The model reproduces the style of what you give it, not an improved version. If your examples have hedging language, the output will too. Give it your best work, not your average work.

**Name what to notice but let the examples do the work.** `"Pay attention to how I open with a concrete observation before stating the broader point"` primes the model. But the example is what actually teaches it. The instruction and the example work together neither alone is as effective as both.

**Use three examples minimum for voice transfer.** One example is a template. Two examples show a pattern. Three examples confirm the pattern is intentional and reduce the chance the model treats an idiosyncrasy in one sample as a rule to follow.

**Show the specific structural unit you care about most.** If your sections always end with a one-sentence payoff line, include a closing paragraph as one of your examples. If your code introductions always follow a specific pattern, show that. Target the structural unit that most defines your voice not just the one that's easiest to share.

**Few-shot is your fastest path to consistent content series.**
If you're publishing weekly, build a set of 3 strong examples once
and reuse them for every piece in the series. The investment is 30
minutes; the return is a consistent voice across every issue without
re-explaining your style each time.

## Related Prompts

- [role-prompting.md](./role-prompting.md) - combine a writer persona with your style examples for maximum voice control
- [chain-of-thought.md](./chain-of-thought.md) - plan the argument first, then use few-shot to execute in your voice
- [prompt-chaining.md](./prompt-chaining.md) - use few-shot as the draft step in a plan → draft → edit pipeline



## Tags

`few-shot` `voice` `style-transfer` `content-series` `ghostwriting` `developer-writing` `writing` `prompt-patterns`