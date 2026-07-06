# Few-Shot Prompting — Analysis

## Description

Teaches the AI your exact analytical output format by showing it concrete
examples before handing over the real data. Instead of describing how you
want findings presented — tone, depth, structure, level of opinion — you
demonstrate it. The model follows the established pattern precisely.

In analysis, format consistency matters as much as accuracy. A finding
written as a vague observation is useless. The same finding written with
a clear signal, a business implication, and a recommended action is
something a team can act on. Few-shot prompting locks in that standard
before the model sees your real data.

## Usage

Provide 2–4 examples that show the input data format and the exact output
style you expect — how findings are framed, how confident the language is,
what level of business context is included, and how recommendations are
phrased. Then give the real data at the end.

Use this pattern for: recurring analysis reports, user feedback synthesis,
survey response coding, metric commentary generation, weekly business
reviews, anomaly flagging, and any analytical task you run repeatedly
where output consistency is as important as the insight itself.

## Prompt

```markdown
I'm going to show you [NUMBER] examples of how I want data analysed
and findings written. Study the pattern carefully — pay attention to
[WHAT TO NOTICE: how findings are framed / confidence level of language /
whether recommendations are included / level of business context].

Then apply the exact same pattern to the new data at the end.

---

## Examples

### Example 1
**Input data:**
[First example dataset or observation]

**Output:**
[First example analysis in your exact preferred style]

---

### Example 2
**Input data:**
[Second example dataset or observation]

**Output:**
[Second example analysis]

---

### Example 3
**Input data:**
[Third example — include one that shows how to handle an edge case,
ambiguous signal, or data with no clear finding]

**Output:**
[Third example analysis showing how to handle ambiguity]

---

## Now apply the pattern

**Input data:**
[Your real data here]

**Output:**
```

## Example Input

```markdown
I'm going to show you 3 examples of how I want user feedback responses
analysed and tagged. Study the pattern carefully — pay attention to how
the sentiment is classified, how the core issue is extracted in one
sentence, how the product area is identified, and how the priority is
assigned based on the language the user uses (not just the sentiment).

Then apply the exact same pattern to the new responses at the end.

---

## Examples

### Example 1
**Input data:**
\```
Response ID: R-1041
Score: 3/10
Text: "The reporting feature is almost there but every time I try to export
a PDF it just hangs. I've tried three times this week and had to screenshot
the dashboard instead. My manager asks for this every Friday and it's
becoming embarrassing."
\```

**Output:**
\```
ID: R-1041
Sentiment: Negative
Core issue: PDF export hangs consistently — user cannot complete a
recurring weekly workflow
Product area: Reporting / Export
Priority: High
Reasoning: Recurring failure on a manager-facing deliverable. "Becoming
embarrassing" signals this is actively damaging the user's professional
credibility — high churn risk if unresolved. Not a nice-to-have: it's
a broken workflow.
Tag: bug, export, reporting, churn-risk
\```

---

### Example 2
**Input data:**
\```
Response ID: R-1042
Score: 8/10
Text: "Love the new dashboard layout. Would be amazing if I could
rearrange the widgets myself — sometimes I want revenue front and centre
and other times I care more about the support queue. Small thing but
would make it perfect."
\```

**Output:**
\```
ID: R-1042
Sentiment: Positive
Core issue: User wants customisable widget order on the dashboard
Product area: Dashboard / Personalisation
Priority: Low
Reasoning: Positive experience with a clear enhancement request.
"Small thing" and "would make it perfect" signal the user is satisfied —
this is an improvement on an already-working feature, not a blocker.
No urgency language present.
Tag: feature-request, dashboard, personalisation
\```

---

### Example 3
**Input data:**
\```
Response ID: R-1043
Score: 6/10
Text: "It's fine. Does what it says. Setup took longer than expected
but we got there in the end. Nothing to complain about really."
\```

**Output:**
\```
ID: R-1043
Sentiment: Neutral
Core issue: Onboarding / setup friction — longer than expected, no
specific blocker identified
Product area: Onboarding
Priority: Medium
Reasoning: Passive satisfaction — "fine" and "nothing to complain about"
are low-engagement signals, not endorsements. Score of 6 places this
user in the passive/detractor boundary. "Setup took longer than expected"
is an onboarding friction signal worth tracking even without specifics —
passive churn risk if a competitor offers easier setup.
Tag: onboarding, setup-friction, passive-user
\```

---

## Now apply the pattern

**Input data:**
\```
Response ID: R-1051
Score: 2/10
Text: "We migrated our entire team to this platform six months ago and
deeply regret it. The permissions system is completely broken — I cannot
figure out how to stop junior team members from accidentally deleting
shared templates. We've lost three important templates this month.
I've submitted two support tickets and haven't heard back in 11 days.
We are actively evaluating alternatives."
\```

---

**Input data:**
\```
Response ID: R-1052
Score: 9/10
Text: "Honestly the best project tool I've used in years. The keyboard
shortcuts alone save me probably 20 minutes a day. My only gripe is that
I can't set a default view — I always have to switch from list to board
view when I open a project. Tiny thing."
\```

---

**Input data:**
\```
Response ID: R-1053
Score: 5/10
Text: "Some things work really well, others not so much. The task
management side is solid but the time tracking feels bolted on —
it doesn't connect to tasks in any meaningful way. I end up using
a separate tool for time tracking which defeats the point."
\```

**Output:**
```

## Example Output

```markdown
ID: R-1051
Sentiment: Negative
Core issue: Permissions system cannot prevent junior users from deleting
shared templates — team has lost data; support unresponsive for 11 days
Product area: Permissions / Access Control + Support
Priority: Critical
Reasoning: Active data loss (three templates deleted) combined with
11-day support silence is a severe trust failure. "Actively evaluating
alternatives" is explicit churn signal — this is not a risk, it is an
imminent loss. Six-month tenure means this is a converted customer
experiencing regression, not a new user with unrealistic expectations.
Requires immediate personal outreach from the support team, not a
ticket response.
Tag: bug, permissions, data-loss, churn-imminent, support-failure, escalate

---

ID: R-1052
Sentiment: Positive
Core issue: User wants a persistent default view setting per project
(list vs board)
Product area: Project View / Personalisation
Priority: Low
Reasoning: Highly satisfied user — score of 9, strong positive framing,
quantified productivity value ("20 minutes a day"). "Tiny thing" confirms
this is a quality-of-life request from a promoter, not a frustration signal.
High NPS contributor — worth acknowledging but not urgently prioritising.
Tag: feature-request, view-settings, personalisation, promoter

---

ID: R-1053
Sentiment: Neutral
Core issue: Time tracking feature feels disconnected from task management —
user is using a separate tool to compensate, reducing platform stickiness
Product area: Time Tracking / Integrations
Priority: Medium
Reasoning: Mid-score user describing a specific integration gap, not a
general complaint. "Defeats the point" signals the user has a clear mental
model of what the product should do and is disappointed it falls short.
Using a competing tool for time tracking is a consolidation risk — if that
tool adds project management, switching cost decreases. Not urgent but
directionally important for retention of users who bought the platform
as an all-in-one solution.
Tag: feature-gap, time-tracking, integration, retention-risk
```

## Variations

| Scenario | What to use as examples |
|---|---|
| Weekly metric commentary | 2–3 metric snapshots with your ideal "so what" narrative |
| Competitor feature comparison | Feature list pairs with your preferred gap analysis format |
| User interview synthesis | Raw interview quotes with your ideal insight + implication format |
| Support ticket categorisation | Ticket text with your preferred category + severity + routing output |
| A/B test result interpretation | Test data with your preferred finding + recommendation format |
| Sales call note analysis | Call transcript excerpts with your preferred objection + signal tagging |
| App store review tagging | Review text with your preferred sentiment + feature area + priority format |

## Tips

**Your examples ARE your style guide.**
You don't need to write a formatting spec — the examples demonstrate it.
If you want findings to lead with the business implication before the
observation, show that in your examples. The model will infer the rule.

**Include one ambiguous or low-signal example.**
Example 3 in the template above — the passive "it's fine" response —
teaches the model how to handle weak signals. Without it, the model
applies your high-signal format to low-signal data and produces
overconfident output.

**The "Reasoning" field is the most important field.**
It forces the model to show its work — why this priority, not that one.
Without it, priority assignments are arbitrary. With it, you can audit
whether the model's logic matches yours and correct it when it doesn't.

**Use few-shot for recurring analysis tasks.**
If you're running the same analysis weekly (feedback tagging, metric
commentary, report generation), invest 30 minutes in building 3 strong
examples once. You'll get consistent output every time rather than
re-explaining your format from scratch each run.

**Calibrate your examples to the hardest cases.**
Easy examples teach easy patterns. If your real data contains edge cases —
ambiguous feedback, conflicting signals, missing context — make sure at
least one example demonstrates how you want those handled.

## Related Prompts

- [role-prompting.md](./role-prompting.md) — prepend an analyst persona to add domain expertise before the examples
- [structured-output.md](./structured-output.md) — enforce a strict JSON schema on top of the few-shot pattern for pipeline-ready output
- [prompt-chaining.md](./prompt-chaining.md) — use few-shot tagged output as structured input to a downstream synthesis step

## Tags

`few-shot` `feedback-analysis` `user-research` `tagging` `consistency` `reporting` `analysis` `prompt-patterns`