# Chain of Thought — Analysis

## Description

Forces the AI to reason through an analytical problem step by step before arriving at a conclusion. Instead of pattern-matching to a familiar answer, the model works through the data systematically forming hypotheses, testing them against the evidence, eliminating weak explanations, and committing to a conclusion it can defend.

In analysis, the difference between a correct insight and a plausible-sounding wrong one is often invisible in the final answer. Chain of thought makes the reasoning visible so you can catch a flawed assumption before it becomes a bad business decision.

## Usage

Present the data or problem clearly, then explicitly instruct the model to
reason step by step before concluding. Define the reasoning stages you want
it to follow — what to look for first, what hypotheses to form, how to
test them, and what the final output should commit to.

Use this pattern for: anomaly investigation, metric drop diagnosis, root
cause analysis on business problems, trend interpretation, conflicting data
reconciliation, and any situation where "why is this happening" matters
more than "what is happening."

## Prompt

```markdown
I need you to reason through the following analytical problem step by step.
Do not jump to a conclusion — work through the evidence at each stage
before moving to the next.

---

## Problem

[Describe the analytical question or anomaly you need to investigate.]

## Data

[Paste the relevant metrics, logs, trends, or observations here.
Include time ranges, baselines, and any context about what changed recently.]

## Background Context

- **Product / System**: [What this data is from]
- **Normal baseline**: [What these numbers usually look like]
- **Recent changes**: [Any deployments, campaigns, pricing changes, or
  external events that could be relevant]
- **What we've already ruled out**: [Hypotheses already investigated]

## Reasoning steps I want you to follow

1. **Restate the problem** — describe the anomaly or question in your
   own words to confirm understanding
2. **Identify what's unusual** — which specific data points deviate
   from baseline and by how much
3. **Generate hypotheses** — list every plausible explanation for
   what you observe, without filtering yet
4. **Test each hypothesis against the data** — for each hypothesis,
   identify the evidence that supports or contradicts it
5. **Eliminate weak explanations** — rule out hypotheses the data
   contradicts, explaining why each is unlikely
6. **Commit to a conclusion** — state the most likely explanation
   with your confidence level and what data would confirm it

## Constraints

- [e.g. "treat all data as accurate — don't suggest data collection errors
  unless the data itself is internally inconsistent"]
- [e.g. "we have not changed pricing or run any campaigns this month"]
- [e.g. "focus on user behaviour causes — infrastructure is monitored
  separately and has no alerts"]
```



## Example Input

```markdown
I need you to reason through the following analytical problem step by step.
Do not jump to a conclusion — work through the evidence at each stage
before moving to the next.

---

## Problem

Our primary conversion metric — free-to-paid upgrades — dropped 34% in
the last 7 days compared to the prior 4-week average. No one on the team
can explain it. I need to understand what is most likely causing this
before our board meeting on Friday.

## Data

\```
Free-to-Paid Conversion Rate (daily):
  Mon 4 weeks ago:  3.8%
  Mon 3 weeks ago:  4.1%
  Mon 2 weeks ago:  3.9%
  Mon last week:    4.0%
  Mon this week:    2.7%   ← drop begins
  Tue this week:    2.5%
  Wed this week:    2.6%
  Thu this week:    2.8%

New free signups (daily average):
  4-week avg:   412/day
  This week:    408/day   ← essentially unchanged

Upgrade page visits (daily average):
  4-week avg:   1,847/day
  This week:    1,203/day ← down 35%

Upgrade page → payment page click-through rate:
  4-week avg:   41%
  This week:    40%       ← unchanged

Payment page → confirmation click-through rate:
  4-week avg:   78%
  This week:    77%       ← unchanged

Trial expiry emails sent (daily):
  4-week avg:   389/day
  This week:    391/day   ← unchanged

Trial expiry email open rate:
  4-week avg:   34%
  This week:    11%       ← down 68%

Trial expiry email click-through rate:
  4-week avg:   18%
  This week:    4%        ← down 78%

In-app upgrade prompt impressions:
  4-week avg:   6,240/day
  This week:    2,180/day ← down 65%
\```

## Background Context

- **Product**: B2B SaaS project management tool, freemium model
- **Normal baseline**: 3.8–4.2% free-to-paid conversion, consistent for 3 months
- **Recent changes**: A/B test on onboarding flow launched Monday this week.
  No pricing changes. No campaigns paused or launched.
- **What we've already ruled out**: Payment processor is functioning normally.
  No increase in failed payments. Support ticket volume is flat.

## Reasoning steps I want you to follow

1. **Restate the problem** — describe the anomaly in your own words
2. **Identify what's unusual** — which data points deviate and by how much
3. **Generate hypotheses** — every plausible explanation without filtering
4. **Test each hypothesis against the data** — support or contradict each one
5. **Eliminate weak explanations** — rule out hypotheses the data contradicts
6. **Commit to a conclusion** — most likely cause, confidence level,
   and what single data point would confirm it

## Constraints

- Treat all data as accurate
- No pricing or campaign changes this month
- Focus on user behaviour and product causes — infrastructure is healthy
```



## Example Output

```markdown
## Step 1 — Restating the Problem

Free-to-paid conversion dropped 34% starting precisely on Monday this week
and has held at that lower level for four consecutive days. The drop is
consistent — not a single-day spike — which rules out a transient event.
The question is: why are fewer free users upgrading to paid, and why did
it start exactly on Monday?

---

## Step 2 — What's Unusual

| Metric | Baseline | This Week | Change |
|---|---|---|---|
| Free-to-paid conversion | 3.9% avg | 2.65% avg | **-34%** |
| Upgrade page visits | 1,847/day | 1,203/day | **-35%** |
| Trial expiry email open rate | 34% | 11% | **-68%** |
| Trial expiry email CTR | 18% | 4% | **-78%** |
| In-app upgrade prompt impressions | 6,240/day | 2,180/day | **-65%** |

**What is NOT unusual:**
- New signups: flat (412 vs 408 — noise level)
- Upgrade page → payment CTR: 41% vs 40% — unchanged
- Payment → confirmation CTR: 78% vs 77% — unchanged
- Trial expiry emails sent: flat

**The critical pattern:** Users are not reaching the upgrade page.
Once they do reach it, conversion behaviour is completely normal.
The funnel is intact from upgrade page onwards — the problem is upstream.

---

## Step 3 — Hypotheses

1. The A/B test on onboarding launched Monday suppressed the in-app
   upgrade prompts for the test variant group
2. The trial expiry email deliverability degraded — emails are being
   sent but going to spam
3. A segment of high-intent users (those near trial expiry) is being
   routed into the new onboarding flow, delaying their upgrade path
4. The in-app upgrade prompt logic has a bug introduced with the A/B
   test deployment that suppresses prompts broadly
5. An external event (competitor promotion, market event) reduced
   upgrade intent across the user base

---

## Step 4 — Testing Each Hypothesis

**Hypothesis 1 — A/B test suppressed upgrade prompts for test variant**
Supporting evidence:
- A/B test launched Monday — exact day the drop began
- In-app upgrade prompt impressions down 65% — not a small dip, a collapse
- Conversion drop is exactly proportional to the drop in upgrade page visits
  (35% fewer visits → 34% fewer conversions — near-perfect correlation)
- The funnel from upgrade page onward is untouched — users who see the
  prompt still convert at the same rate

Verdict: Strongly supported. The data fingerprint matches precisely.

**Hypothesis 2 — Email deliverability degraded**
Supporting evidence:
- Trial expiry email open rate down 68% — severe
- Email CTR down 78%

Contradicting evidence:
- Emails sent volume is unchanged — the sending infrastructure is working
- But 68% open rate drop in one day is more consistent with a spam filter
  change or domain reputation issue than with a campaign change
- However: in-app prompt impressions are also down 65% — email alone
  cannot explain both drops simultaneously

Verdict: Partially supported for the email channel specifically.
Cannot explain the in-app prompt drop. Not the root cause — possibly
a concurrent secondary issue.

**Hypothesis 3 — High-intent users routed into new onboarding, delaying upgrade**
Supporting evidence:
- A/B test affects new onboarding — could delay upgrade prompts for
  users in the test variant

Contradicting evidence:
- New signups this week are flat (408/day) — if the test only affects
  new users, it cannot explain a 65% drop in upgrade prompt impressions
  for the existing user base
- Trial expiry users are existing users, not new signups — they would
  not be in a new onboarding flow

Verdict: Weak. Cannot explain the scale of the drop across both
email and in-app channels for existing users.

**Hypothesis 4 — A/B test deployment introduced a bug suppressing upgrade prompts broadly**
Supporting evidence:
- Prompt impressions down 65% — affects the full user base, not just
  a test segment
- Email open rate down 68% — if the same deployment affected email
  sending logic or audience segmentation, both drops are explained
- Monday deployment → Monday drop onset — timing is exact

Contradicting evidence:
- None in the available data

Verdict: Strongly supported. A deployment bug that accidentally
suppressed upgrade prompts AND corrupted trial expiry email audience
segmentation explains all anomalies simultaneously.

**Hypothesis 5 — External market event reduced upgrade intent**
Supporting evidence: None

Contradicting evidence:
- New signups are flat — if the market shifted, acquisition would also drop
- Once users reach the upgrade page, conversion is unchanged — their
  intent is intact
- The drop is mechanical (impressions down → visits down → conversions down),
  not motivational (intent change would show up as conversion rate dropping
  while visits held steady)

Verdict: Ruled out. The data pattern is mechanical, not behavioural.

---

## Step 5 — Eliminating Weak Explanations

- **Hypothesis 3 (onboarding rerouting)** — eliminated. Cannot explain
  drops in existing user upgrade prompts or email engagement at this scale.
- **Hypothesis 5 (external event)** — eliminated. Contradicted by flat
  signups and unchanged post-visit conversion rates.
- **Hypothesis 2 (email deliverability)** — not eliminated but demoted
  to secondary issue. It explains the email channel only. The in-app
  prompt collapse requires a different cause.

---

## Step 6 — Conclusion

**Most likely cause: The A/B test deployment on Monday introduced a bug
that suppressed upgrade prompt delivery across both in-app and email
channels for a significant portion of the user base.**

Confidence: High (85%)

The evidence is mechanical and consistent — impressions collapsed,
visits followed proportionally, conversion held steady once reached.
This is not a user behaviour change. It is a delivery failure.

**The single data point that would confirm this:**
Check the A/B test variant assignment logic — specifically whether
trial-expiry users and in-app upgrade prompt audiences were accidentally
included in an exclusion rule meant for the onboarding test variant.
If variant B users have a flag that suppresses upgrade prompts,
you will find it there.

**Immediate action:**
Pause the A/B test and restore the control experience. If conversion
returns to 3.8–4.2% within 24 hours, the hypothesis is confirmed.
Do not wait for Friday — four days of suppressed upgrades at your
baseline volume is a material revenue impact.
```



## Variations


| Scenario                     | Adapt the prompt by...                                                           |
| ---------------------------- | -------------------------------------------------------------------------------- |
| Revenue metric drop          | Including MRR trend, churn rate, and expansion revenue alongside conversion data |
| Feature adoption anomaly     | Adding feature usage cohorts and release changelog as context                    |
| API error rate spike         | Including error codes, affected endpoints, and deployment history                |
| Support ticket surge         | Categorising ticket topics and mapping them to recent product changes            |
| Funnel stage drop            | Providing step-by-step funnel metrics with timestamps of any changes             |
| Churn spike investigation    | Including cohort data, cancellation reasons, and NPS trends                      |
| Ad campaign underperformance | Adding impression, CTR, CPC, and ROAS data by channel and creative               |




## Tips

**"Do not jump to a conclusion" is the most important instruction.**
Without it, the model pattern-matches to the most common explanation
and dresses it up as reasoning. With it, you get genuine hypothesis
elimination — which is what catches non-obvious root causes.

**The "what's unusual" step surfaces the real signal.**
Ask the model to explicitly list what is NOT unusual alongside what is.
In the example above, the unchanged post-visit conversion rate is as
important as the collapsed prompt impressions — it rules out intent-based
causes entirely.

**Include recent changes as context, not as hypotheses.**
Don't say "was it the A/B test?" — let the model discover that connection
itself. Feeding it as a hypothesis biases the reasoning. Feeding it as
background context lets the model weigh it objectively.

**"What data would confirm it" is the most actionable output.**
The final step should always produce a single investigative action —
a query to run, a metric to check, a log to inspect. This converts
analysis into next steps immediately.

**Correlate timing precisely.**
The most useful thing you can include is "the drop started on [date]"
paired with "we deployed [X] on [date]." Exact timing correlation is
often the fastest path to a root cause.

## Related Prompts

- [role-prompting.md](./role-prompting.md) — assign a data analyst or SRE persona to frame the reasoning perspective
- [structured-output.md](./structured-output.md) — format the hypothesis elimination table as structured JSON for reporting pipelines
- [prompt-chaining.md](./prompt-chaining.md) — use CoT output as the investigation stage in a multi-step analysis pipeline



## Tags

`chain-of-thought` `root-cause-analysis` `metric-drops` `anomaly-detection` `hypothesis-testing` `analysis` `prompt-patterns`