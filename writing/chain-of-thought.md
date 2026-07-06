# Chain of Thought - Writing

## Description

Forces the AI to reason through the structure, argument, and angle of a piece before writing a single word. Instead of generating content directly from a brief which produces the first plausible interpretation of your topic, not the best one the model works through the editorial decisions systematically: what is the real argument, who is the reader, what do they already believe, what needs to shift, and what is the most effective structure to make that happen.

In writing, the difference between a forgettable piece and one that gets shared is almost always a decision made before the first sentence the angle, the opening hook, the argument arc. Chain of thought surfaces those decisions explicitly so you can correct them before they become 1,500 words of content built on the wrong foundation.

## Usage

Give the model your topic, your audience, and your goal. Then instruct it to reason through the editorial decisions step by step before proposing an angle or structure. Define the reasoning stages explicitly what question to answer at each stage so the model doesn't collapse the thinking into a single pass.

Use this pattern for: finding the right angle on a familiar topic, planning
argument structure for opinion pieces, resolving a brief that could go in
multiple directions, stress-testing a headline before committing to it,
and any writing task where the "what to say" decision is harder than the
"how to say it" execution.

## Prompt

```markdown
I need you to reason through the editorial decisions for the following
writing task step by step. Do not start writing the content yet — work
through the thinking first.

---

## Writing Task

[Describe what needs to be written — format, publication, audience,
and any constraints on length or tone.]

## Topic

[The subject matter you want to write about.]

## Goal

[What the piece needs to accomplish — inform, persuade, convert,
build credibility, drive shares, etc.]

## What I already know about the audience

[Their background, what they already believe about this topic,
what objections they might have, and what would make them share or act.]

## Reasoning steps I want you to follow

1. **Identify the real argument** — strip the topic down to its single
   most defensible claim. Not a subject area, not a question — a claim
   the reader could agree or disagree with.

2. **Audit the audience's current belief** — what does the target reader
   already think about this topic? What assumption needs to be challenged
   or confirmed to move them?

3. **Find the angle** — given the argument and the audience's prior belief,
   what is the most surprising or counterintuitive way into this topic?
   What angle makes a familiar subject feel worth reading?

4. **Stress-test the headline** — generate 3 headline options for this
   angle. For each, state who it will resonate with and who it will lose.

5. **Design the argument arc** — what does the reader need to believe at
   the end of each section to be ready for the next one? Map the
   logical sequence from opening assumption to final conclusion.

6. **Identify the biggest risk** — what is the most likely way this piece
   fails? Where could the argument break down, the reader disengage,
   or the conclusion feel unearned?

## Constraints

- [e.g. "the piece must not feel like a tutorial — it should feel like
  a perspective piece from a practitioner"]
- [e.g. "the reader is sceptical of AI hype — avoid enthusiasm framing"]
- [e.g. "under 1,200 words — the argument must be tight"]
```



## Example Input

```markdown
I need you to reason through the editorial decisions for the following
writing task step by step. Do not start writing the content yet — work
through the thinking first.

---

## Writing Task

A LinkedIn article for my personal profile. Audience: senior software
engineers, engineering managers, and technical leads at product companies.
Length: 600–800 words. Published as a LinkedIn native article, not a link
to an external blog.

## Topic

Why prompt engineering is a real engineering discipline — not a temporary
skill that will become obsolete when models improve.

## Goal

Build credibility as an AI/LLM practitioner, generate discussion in the
comments, and position myself as someone worth following for practical
AI engineering content. Secondary goal: drive traffic to the promptblocks
GitHub repo.

## What I already know about the audience

Senior engineers are sceptical of AI hype. Many of them have seen
"prompt engineering" dismissed as "just talking to a chatbot" or as a
temporary hack that will be automated away by better models. Some have
experimented with LLMs and found the results inconsistent — which
reinforces their scepticism. They respond to peer-to-peer arguments from
practitioners, concrete examples, and intellectual honesty about limitations.
They disengage immediately from enthusiasm-first framing or anything that
sounds like a product pitch.

## Reasoning steps I want you to follow

1. **Identify the real argument** — the single most defensible claim
2. **Audit the audience's current belief** — what they already think
   and what needs to shift
3. **Find the angle** — the most counterintuitive way into this topic
4. **Stress-test the headline** — 3 options with audience analysis for each
5. **Design the argument arc** — section-by-section belief map
6. **Identify the biggest risk** — where this piece most likely fails

## Constraints

- Must not feel like AI evangelism — the reader is sceptical and will
  disengage at the first sign of hype
- Must acknowledge the "models will get better" objection directly —
  not dodge it
- The repo mention should feel earned, not promotional
```



## Example Output

```markdown
## Step 1 — The Real Argument

**Claim:** Prompt engineering is not about knowing the right magic words —
it is about understanding how to decompose a problem for a system that
reasons differently than a human does. That skill does not become obsolete
when models improve; it becomes more valuable, because better models
amplify good problem decomposition and forgive less of the bad kind.

This is specific enough to disagree with. A sceptic could counter: "Better
models will decompose problems themselves." That counter-argument exists,
which means the claim is worth making.

---

## Step 2 — Audience Belief Audit

**What they currently believe:**
- "Prompt engineering is a temporary workaround for model limitations"
- "Better models will make prompts irrelevant — GPT-5 just knows what you mean"
- "Real engineers don't need to learn how to talk to a chatbot"

**What needs to shift:**
The reader needs to see that prompt engineering is not about compensating
for model weakness — it is about structuring information for a system that
processes it differently than a database, a compiler, or a human colleague.
The analogy is not "writing search queries better." The analogy is
"designing an API contract for a non-deterministic system."

**What will NOT work:**
Claiming prompt engineering is "just like" any existing engineering skill
— it will read as defensive. Claiming it will never change — it will read
as naïve. The honest argument is: the fundamentals are durable even as
the surface changes.

---

## Step 3 — The Angle

**Most counterintuitive framing:**
The piece should argue that improving models make prompt engineering
*more* important, not less — because a powerful model given a vague
prompt produces a confident, detailed, wrong answer. A weak model fails
visibly. A strong model fails invisibly.

This inverts the sceptic's assumption. They believe model improvements
are a threat to the skill. The piece argues they are a reason to take
it more seriously.

**Secondary angle worth threading in:**
Design patterns analogy — Gang of Four didn't become obsolete when
compilers improved. The abstractions got more useful because the
underlying systems got more capable.

---

## Step 4 — Headline Stress-Test

**Option A:** "Prompt Engineering Won't Become Obsolete. Better Models
Make It More Important."
- Resonates with: engineers already experimenting with LLMs who've
  noticed inconsistency at scale
- Loses: hard sceptics — the headline sounds like a defensive claim
  and may not earn the click from someone who already disagrees

**Option B:** "Why Senior Engineers Should Stop Dismissing Prompt Engineering"
- Resonates with: the exact audience — "senior engineers" in the headline
  is a direct address; "stop dismissing" implies the reader might be
  making a mistake they don't know about
- Loses: readers who don't self-identify as dismissive — they may not
  feel addressed
- Risk: sounds confrontational; needs the body to be intellectually
  honest to earn the provocative framing

**Option C:** "The Model Got Smarter. Your Prompt Got Worse."
- Resonates with: engineers who have experienced confident-wrong outputs
  from GPT-4 class models — the headline names a real experience
- Loses: readers who haven't hit this yet — the premise may not land
- Strength: most specific and most counterintuitive of the three;
  creates a "wait, what?" reaction that drives clicks from the right audience

**Recommendation:** Option C as the primary headline with Option B as
the LinkedIn post teaser copy. Option C earns the click; Option B frames
the stakes in the body.

---

## Step 5 — Argument Arc

**Opening (what reader believes entering):**
"Better models will make prompt engineering obsolete."

**Section 1 — Acknowledge the objection honestly:**
Reader needs to believe: "This author understands my scepticism and isn't
going to dismiss it."
Content: Yes, models are getting better at interpreting vague input.
No, that doesn't make structured prompting less valuable — here's why.

**Section 2 — Reframe what prompt engineering actually is:**
Reader needs to believe: "I've been thinking about this wrong."
Content: It is not about magic words. It is about decomposing a complex
problem into a sequence of steps a non-deterministic reasoning system
can execute reliably. That's an engineering problem.

**Section 3 — The counterintuitive argument:**
Reader needs to believe: "A more powerful model actually raises the stakes."
Content: A weak model fails visibly — you know when it's wrong.
A powerful model fails confidently — it produces a detailed, well-structured,
wrong answer you might ship. Better models require more precise problem
decomposition, not less.

**Section 4 — The design patterns analogy:**
Reader needs to believe: "This is a durable skill, not a temporary one."
Content: Gang of Four design patterns didn't become obsolete when
compilers improved. The patterns became more useful because the
underlying systems got more capable. Same dynamic.

**Conclusion — The repo earn:**
Reader needs to believe: "There's something practical I can use right now."
Content: The five patterns I use across every AI feature I build —
link to promptblocks. Not a pitch; a resource from a practitioner
who just made a compelling argument.

---

## Step 6 — Biggest Risk

**Most likely failure mode: the argument feels like motivated reasoning.**

Senior engineers will suspect the author has something to sell
(their AI skills, their repo, their personal brand) and is constructing
a post-hoc argument for why prompt engineering matters. The "design
patterns" analogy can read as self-serving if it arrives too early —
before the sceptical reader has had their objection acknowledged.

**How to mitigate:**
Lead with the concession — explicitly acknowledge that models are getting
better and the sceptics are not entirely wrong. Open by agreeing with
part of their belief before redirecting it. Engineers trust intellectual
honesty more than confident claims. A writer who acknowledges the strongest
counter-argument before making their case earns the credibility to make it.
```



## Variations


| Scenario              | Adapt the prompt by...                                                                                     |
| --------------------- | ---------------------------------------------------------------------------------------------------------- |
| Opinion piece         | Focus Step 3 on the most contrarian defensible position                                                    |
| Tutorial / how-to     | Replace argument arc with learning arc — what does reader need to know at each step to understand the next |
| Newsletter issue      | Add a "what happened this week that makes this timely" step before the angle                               |
| Case study            | Include a "what is the specific claim the data supports" step before structuring                           |
| Thread / microcontent | Add a "what is the single sharpest sentence in this argument" step for the hook                            |
| SEO article           | Add a "what does the reader intend when they search this query" step before the angle                      |




## Tips

**"Do not start writing yet" changes everything.** Without this instruction, the model starts drafting immediately producing the most familiar interpretation of your topic. The reasoning steps only work if writing is explicitly deferred until thinking is complete.

**Step 1 - the real argument is the hardest and most important.** Most briefs give you a topic, not an argument. "AI in software development" is a topic. "AI-assisted code review catches more logic errors than style errors, which inverts how most teams use it" is an argument. Push the model to produce a claim someone could disagree with, then you have something worth writing.

**Stress-testing headlines before writing saves hours.** A headline analysis that takes 5 minutes of prompting can save an hour of writing in the wrong direction. If none of the three headline options are compelling, the angle is wrong, fix it before writing a word.

**The "biggest risk" step is editorial quality control.** Asking the model to identify where the piece most likely fails surfaces structural weaknesses before they become published content. A weak counter-argument, an unearned conclusion, a section that loses the sceptical reader, these are easier to fix at the planning stage.

**Use this as Step 1 of a writing chain.** The output of this reasoning step the angle, the argument arc, the recommended headline becomes the structured brief for the next step: the actual draft. Pass it as context to a role-prompted writing step and the draft will be far more focused than anything produced from a raw brief.

## Related Prompts

- [role-prompting.md](./role-prompting.md) - assign a writer persona once the argument is planned
- [few-shot.md](./few-shot.md) - show examples of your preferred article style after the angle is locked
- [prompt-chaining.md](./prompt-chaining.md) - use this CoT output as the structured brief for a full draft → edit chain



## Tags

`chain-of-thought` `editorial-planning` `content-strategy` `argument-structure` `linkedin` `writing` `prompt-patterns`