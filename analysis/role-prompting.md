# Role Prompting — Analysis

## Description

Assigns a specialist analytical persona to the AI before issuing an analysis
task. By defining a specific analyst role with domain expertise, methodology
bias, and professional context, you get responses that reflect how a real
expert in that field would approach the problem — not a generic summary.

The same dataset analysed by a "data scientist" vs a "product manager" vs a
"security auditor" produces fundamentally different insights. Role prompting
lets you choose the analytical lens before you hand over the data.

## Usage

Define the analyst role with seniority, domain specialisation, and the
organisation type they work in. Then describe the analysis task with clear
instructions on what kind of output you need — patterns, anomalies, risks,
recommendations, or a combination.

Use this pattern for: user behaviour analysis, performance metric review,
error log triage, survey response interpretation, competitor analysis,
system health assessment, and any task where the analytical perspective
shapes what gets noticed and what gets ignored.

## Prompt

```markdown
You are a [SENIORITY] [ANALYST ROLE] with [X] years of experience in
[DOMAIN / INDUSTRY SPECIALISATION].
You work at [TYPE OF ORGANISATION — e.g. B2B SaaS company, data-driven
e-commerce platform, cybersecurity firm].

Your analytical approach is [STYLE — e.g. hypothesis-driven / pattern-first /
risk-focused / user-centric]. You prioritise [WHAT MATTERS MOST — e.g.
actionable findings over completeness / statistical rigour over speed].
Assume I have [LEVEL] familiarity with [DOMAIN].

---

## Task

[Describe what you want analysed and what kind of output you need.]

## Data

[Paste the raw data, metrics, logs, survey responses, or content to analyse.]

## What I need from you

1. **[Output section 1]** — [what it should contain]
2. **[Output section 2]** — [what it should contain]
3. **[Output section 3]** — [what it should contain]

## Constraints

- [What to focus on — e.g. "focus on retention signals, not acquisition"]
- [What to ignore — e.g. "ignore data before January — it's from a different product"]
- [Output constraints — e.g. "flag only findings that are actionable within 30 days"]
```



## Example Input

```markdown
You are a senior product analyst with 8 years of experience in B2B SaaS
growth and retention analytics. You work at a mid-stage startup with a
product-led growth motion.

Your analytical approach is hypothesis-driven and you look for the signal
behind the metric, not just the metric itself. You prioritise actionable
findings that a product team can act on in the next sprint over
exhaustive statistical coverage.
Assume I have intermediate familiarity with product analytics.

---

## Task

Analyse the following 30-day user activity metrics for our project
management SaaS. Identify retention risks, engagement patterns, and
the most important thing the product team should act on this sprint.

## Data

\```
Monthly Active Users: 3,842
Daily Active Users (avg): 614
DAU/MAU Ratio: 15.9%

Feature Usage (% of MAU who used each feature at least once):
- Task creation:        91%
- Task assignment:      74%
- Due date setting:     61%
- Comment / mention:    38%
- File attachment:      29%
- Dashboard view:       22%
- Recurring tasks:      8%
- API integrations:     6%

User Cohort Retention (by signup month):
- Month 0 (current):   100%
- Month 1:             61%
- Month 2:             44%
- Month 3:             39%
- Month 4:             38%
- Month 5:             38%
- Month 6+:            37%

Support Tickets (last 30 days): 214 total
Top categories:
- "How do I..." (how-to):        38%
- Billing questions:             21%
- Bug reports:                   19%
- Feature requests:              14%
- Account/access issues:         8%

NPS Score: 31
Promoters (9–10): 28%
Passives  (7–8):  47%
Detractors (0–6): 25%
\```

## What I need from you

1. **Key findings** - the 3–5 most important signals in this data,
   with your interpretation of what each one means for the business
2. **Retention risk assessment** — where is the biggest drop-off
   and what is the most likely cause
3. **One priority recommendation** — the single highest-leverage
   action the product team should take this sprint, with your reasoning

## Constraints

- Focus on retention and engagement signals — we have a separate
  team handling billing and acquisition
- Flag only findings the product team can act on — not infrastructure
  or sales concerns
- Be direct — give an opinion, don't hedge everything with "it depends"
```



## Example Output

```markdown
## Key Findings

**1. The product has a core loop problem, not a feature problem.**
DAU/MAU of 15.9% means users open the product fewer than 5 days per month
on average. For a project management tool — a category where daily use is
the value proposition — this is a fundamental engagement failure. Adding
features won't fix it. The product isn't part of users' daily work habit yet.

**2. Retention stabilises at Month 3 — but at a dangerously low floor.**
The cohort data shows a steep drop from Month 1 (61%) to Month 2 (44%),
then stabilises around 37–39% from Month 3 onward. The good news: you have
a loyal core that doesn't churn. The bad news: you're losing more than half
your users before they reach that stable state. The Month 1→2 window is
where the product is failing to convert trial users into habitual ones.

**3. Collaboration features are severely underused relative to core features.**
Task creation (91%) and assignment (74%) are healthy — users understand
the basic workflow. But comments/mentions (38%), file attachments (29%),
and dashboard views (22%) are all low. These are the features that make
a team sticky — when multiple people depend on the same tool, churn becomes
harder. Low adoption here means most users are using this as a personal
to-do app, not a team collaboration platform. Single-user tools churn.

**4. 38% of support tickets are "how do I" questions.**
This is a discoverability and onboarding signal, not a support signal.
Users want to do things the product can do — they just can't find how.
Combined with the low adoption of advanced features, this suggests the
product's information architecture is hiding its own value.

**5. NPS of 31 with 47% passives is a conversion opportunity.**
Passives are not loyal — they'll switch when a competitor runs a promotion.
But they're also not actively unhappy — they just don't love the product yet.
At 47% of your base, converting even a third of them to promoters would
dramatically shift your NPS and word-of-mouth growth.

---

## Retention Risk Assessment

**Biggest drop-off:** Month 1 → Month 2 (61% → 44% — a 17-point loss).

**Most likely cause: Failed activation into team workflows.**
Users sign up, create tasks (91% do), and explore the product solo. But
they never successfully pull their team in — comments and mentions sit at
38%, meaning most users never experience the collaborative value that
justifies a paid subscription over a free to-do app.

The pattern: user creates tasks → doesn't invite team or team doesn't engage
→ product feels like extra work rather than replaced work → user churns by
Month 2 when the novelty fades.

This is an activation problem disguised as a retention problem. The users
who make it to Month 3 (39%) are almost certainly the ones who successfully
adopted it with their team — hence the stable floor.

---

## One Priority Recommendation

**Build a team activation nudge into the first 14 days.**

The data points to one root cause: users who don't collaborate don't stay.
The highest-leverage action this sprint is an in-product nudge that triggers
when a user has created 3+ tasks but has zero comments, mentions, or
assignments to other users.

The nudge should do one thing: get a second person into the product.

A simple implementation:
- Trigger: 3+ tasks created, 0 collaboration events, Day 3–7 of account
- Message: "Projects move faster with your team — invite a teammate to
  [most recent project name]"
- Action: Single-click invite by email, pre-filled with the project context

Why this over everything else: you don't need more features — you need
more of your existing users to experience the feature that makes them stay.
The 38% who use comments/mentions are almost certainly in your retained cohort.
Get more users there faster and Month 1→2 retention will improve within
60 days — fast enough to measure in the next two sprint cycles.
```



## Variations


| Scenario                   | Role to Use                                                               |
| -------------------------- | ------------------------------------------------------------------------- |
| User behaviour analysis    | `senior product analyst specialising in B2B SaaS retention`               |
| Error log triage           | `site reliability engineer with expertise in distributed systems`         |
| Survey response analysis   | `UX researcher specialising in qualitative data synthesis`                |
| Financial metric review    | `financial analyst at a venture-backed startup`                           |
| Security log audit         | `security analyst specialising in threat detection and incident response` |
| Competitor analysis        | `product strategist with experience in competitive intelligence`          |
| Marketing funnel review    | `growth analyst specialising in conversion rate optimisation`             |
| API usage pattern analysis | `developer experience analyst at a platform company`                      |




## Tips

**The role determines what gets noticed.**
A data scientist will look for statistical significance.
A product manager will look for user behaviour patterns.
A security auditor will look for anomalies and outliers.
The same data produces different insights depending on the lens.
Choose the role that matches the decision you need to make.

**"Prioritise actionable findings" changes the output entirely.**
Without this instruction, the model gives you a balanced report covering
everything. With it, you get a ranked list of things to act on — which
is almost always what you actually need.

**Give the analyst a strong opinion bias.**
`"Be direct — give an opinion, don't hedge everything with 'it depends'"` is
the most important constraint you can add to an analysis prompt. Hedged
analysis paralysis is the default. Opinionated analysis is the goal.

**Context about what to ignore is as important as what to analyse.**
`"Ignore data before January — it's from a different product"` or
`"Focus on retention signals, not acquisition"` prevents the model from
spending half its output on things you already know or don't care about.

**Include the "so what" in your output request.**
Don't just ask for findings — ask for the implication of each finding.
`"What does each finding mean for the business"` produces insight.
`"Summarise the data"` produces a description.

## Related Prompts

- [chain-of-thought.md](./chain-of-thought.md) — pair with role prompting for multi-step analytical reasoning
- [structured-output.md](./structured-output.md) — enforce a structured schema on analysis output for dashboards or reporting pipelines
- [prompt-chaining.md](./prompt-chaining.md) — use role-prompted analysis as the first step in a multi-stage insight pipeline



## Tags

`role-prompting` `product-analytics` `retention` `user-behaviour` `data-analysis` `analysis` `prompt-patterns`