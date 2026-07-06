# Prompt Chaining - Analysis

## Description

Breaks a complex analytical task into a sequence of focused steps where the output of each stage becomes the structured input of the next. Instead of asking the model to collect, interpret, synthesise, and recommend all at once, you decompose the analysis into stages each one with a single responsibility and a verifiable output.

In analysis, chaining solves the core quality problem of single-prompt
approaches: when you ask for everything at once, the model skips the hard
reasoning to reach the conclusion faster. Chaining forces depth at every
stage. Bad assumptions surface at Step 2, not after you've presented the
findings to a stakeholder.

## Usage

Map the natural stages of your analysis where does data need to be cleaned before it can be interpreted? Where does interpretation need to finish before recommendations can be made? Each stage boundary is a chain link.

Pass structured JSON between every step. Prose output from Step 2 as input to Step 3 reintroduces ambiguity. Typed JSON output means the next step receives reliable, parseable data and you can inspect and correct at any stage before the error compounds.

Use this pattern for: multi-source research synthesis, quarterly business
reviews, churn investigation pipelines, competitive landscape reports,
user research synthesis, and any analytical task complex enough that a
single prompt produces a shallow or inconsistent result.

## Prompt

```markdown
## Step [N] of [TOTAL] — [STEP NAME]

### What this step does
[One sentence describing the single responsibility of this step.]

### Input from previous step
[Paste the output of the previous step here — or the raw source data
if this is Step 1.]

\```[format]
[Previous step output or initial raw input]
\```

### Your task
[Focused instruction for this step only. Do not ask it to interpret,
synthesise, or recommend if this is a collection or cleaning step.]

### Output format
[Exact schema, structure, or format this step must return so the next
step can consume it without ambiguity.]

### Rules
- Focus only on this step — do not perform work that belongs to a later stage
- [Constraints specific to this step]
- Return [FORMAT] only — no explanation unless explicitly requested
```



## Example - Full 4-Step Chain

A pipeline that takes raw multi-source customer churn data and produces an actionable churn reduction playbook broken into: collect → diagnose → prioritise → prescribe.

---



### Step 1 of 4 - Collect and Normalise

```markdown
## Step 1 of 4 — Collect and Normalise

### What this step does
Extract and normalise churn signals from multiple raw data sources into
a single structured format for analysis.

### Input
\```
SOURCE 1 — Exit survey responses (last 90 days, n=143):
"Too expensive for what it does" — 31 responses
"Missing feature: Slack integration" — 24 responses
"Switched to Notion" — 19 responses
"Too complicated, team didn't adopt it" — 17 responses
"We downsized, don't need it anymore" — 14 responses
"Better option found" — 12 responses
"Slow performance on large projects" — 11 responses
"Didn't use it enough to justify cost" — 9 responses
"Poor customer support experience" — 6 responses

SOURCE 2 — Churned account data (last 90 days):
Total churned accounts: 143
Churned MRR: $28,400
Average account tenure at churn: 4.2 months
Plan breakdown: Starter 61%, Pro 29%, Business 10%
Top churned features used: Task management (89%), Comments (41%),
Reporting (18%), API (9%)

SOURCE 3 — Support tickets from churned accounts (60 days pre-churn):
Avg tickets per churned account: 3.8
Ticket categories: How-to (44%), Bug reports (29%), Billing (18%),
Feature requests (9%)
Avg first response time for churned accounts: 31 hours
Avg resolution time: 4.7 days

SOURCE 4 — Product usage (30 days pre-churn):
Avg DAU in final 30 days: 1.2 (vs 3.1 for retained accounts)
Feature adoption gap vs retained: Comments (-38pp), Reporting (-52pp),
API (-41pp)
Last login before churn: 78% had not logged in for 14+ days
\```

### Your task
Normalise all four sources into a unified set of churn signal categories.
Group related signals across sources, deduplicate overlapping themes,
and assign a weight to each category based on the volume and financial
impact of signals pointing to it.

Do not interpret causes or recommend actions yet — only collect and
structure the signals.

### Output format
Return a JSON array only — no explanation.

\```typescript
{
  churnSignals: {
    category: string               // concise category name
    sources: string[]              // which sources contributed to this signal
    evidencePoints: string[]       // key data points from each source
    affectedAccountsPct: number    // estimated % of churned accounts this applies to
    mrrAtRisk: number              // estimated churned MRR attributable to this category
    weight: "primary" | "secondary" | "contributing"
  }[]
  totalChurnedAccounts: number
  totalChurnedMrr: number
  analysisPeriodDays: number
}
\```

### Rules
- Do not merge unrelated signals just to reduce category count
- affectedAccountsPct and mrrAtRisk are estimates — use proportional
  reasoning from the available data
- weight: "primary" = dominant driver, "secondary" = significant contributor,
  "contributing" = present but not decisive
- Return raw JSON only
```

**Step 1 Output:**

```json
{
  "churnSignals": [
    {
      "category": "Perceived value gap — price vs utility",
      "sources": ["Exit survey", "Product usage"],
      "evidencePoints": [
        "31 exit responses cited 'too expensive for what it does'",
        "9 responses cited 'didn't use it enough to justify cost'",
        "78% of churned accounts had not logged in for 14+ days pre-churn — low engagement undermines perceived value"
      ],
      "affectedAccountsPct": 28,
      "mrrAtRisk": 7952,
      "weight": "primary"
    },
    {
      "category": "Low team adoption — product never became a habit",
      "sources": ["Exit survey", "Product usage", "Support tickets"],
      "evidencePoints": [
        "17 exit responses cited 'too complicated, team didn't adopt it'",
        "Avg DAU in final 30 days: 1.2 vs 3.1 for retained — 61% lower",
        "Comments adoption gap: -38pp vs retained accounts",
        "44% of pre-churn support tickets were how-to questions — users couldn't find features"
      ],
      "affectedAccountsPct": 34,
      "mrrAtRisk": 9656,
      "weight": "primary"
    },
    {
      "category": "Missing integrations — Slack gap",
      "sources": ["Exit survey"],
      "evidencePoints": [
        "24 exit responses cited missing Slack integration as a reason for leaving",
        "Slack-connected workflows are likely driving users to competing tools"
      ],
      "affectedAccountsPct": 17,
      "mrrAtRisk": 4828,
      "weight": "secondary"
    },
    {
      "category": "Support experience failure",
      "sources": ["Support tickets", "Exit survey"],
      "evidencePoints": [
        "Avg first response time for churned accounts: 31 hours",
        "Avg resolution time: 4.7 days",
        "6 exit responses cited poor support experience",
        "29% of pre-churn tickets were bug reports — unresolved bugs with slow support accelerates churn"
      ],
      "affectedAccountsPct": 22,
      "mrrAtRisk": 6248,
      "weight": "secondary"
    },
    {
      "category": "Competitive displacement — Notion",
      "sources": ["Exit survey"],
      "evidencePoints": [
        "19 exit responses explicitly cited switching to Notion",
        "Notion competes on simplicity and all-in-one positioning"
      ],
      "affectedAccountsPct": 13,
      "mrrAtRisk": 3692,
      "weight": "secondary"
    },
    {
      "category": "External — downsizing / budget cuts",
      "sources": ["Exit survey"],
      "evidencePoints": [
        "14 exit responses cited downsizing as the reason — not product-related"
      ],
      "affectedAccountsPct": 10,
      "mrrAtRisk": 2840,
      "weight": "contributing"
    }
  ],
  "totalChurnedAccounts": 143,
  "totalChurnedMrr": 28400,
  "analysisPeriodDays": 90
}
```

---



### Step 2 of 4 - Diagnose Root Causes

```markdown
## Step 2 of 4 — Diagnose Root Causes

### What this step does
Take the normalised churn signals and identify the underlying root causes —
the structural product, commercial, or operational failures that produced
each signal.

### Input from Step 1
\```json
[paste Step 1 output here]
\```

### Your task
For each primary and secondary churn signal, diagnose the root cause.
Distinguish between surface symptoms (what users say) and structural
causes (what the product or business is actually failing at).
Identify whether each cause is product, commercial, operational, or
external. Do not recommend solutions yet.

### Output format
Return a JSON object only — no explanation.

\```typescript
{
  rootCauses: {
    signal: string                 // category name from Step 1
    symptom: string                // what users experience or report
    rootCause: string              // the underlying structural failure
    causeType: "product" | "commercial" | "operational" | "external"
    evidenceSummary: string        // why this diagnosis fits the data
    compoundingFactors: string[]   // other signals that make this cause worse
  }[]
  primaryFailureMode: string       // the single most important structural problem
  secondaryFailureMode: string     // the second most important structural problem
}
\```

### Rules
- Exclude "contributing" weight signals — focus on primary and secondary only
- rootCause must describe a structural failure, not restate the symptom
- Return raw JSON only
```

**Step 2 Output:**

```json
{
  "rootCauses": [
    {
      "signal": "Low team adoption — product never became a habit",
      "symptom": "Teams find the product too complex and fail to build a daily usage habit",
      "rootCause": "Onboarding does not guide teams through a collaborative activation milestone — users create tasks solo but never successfully pull teammates into a shared workflow, so the product never delivers its core team value",
      "causeType": "product",
      "evidenceSummary": "DAU 61% lower than retained accounts in final 30 days; comments adoption -38pp vs retained; 44% of pre-churn tickets were how-to questions — users wanted to use features but couldn't find them. The pattern matches failed activation, not product rejection.",
      "compoundingFactors": [
        "Support response time of 31 hours means users who get stuck during onboarding wait too long for help and disengage before resolution",
        "Perceived value gap worsens when users haven't adopted enough features to feel the product earns its price"
      ]
    },
    {
      "signal": "Perceived value gap — price vs utility",
      "symptom": "Users feel the product costs more than the value they receive",
      "rootCause": "Low feature adoption means users never experience the full product value, making price feel disproportionate — this is a value realisation failure, not a pricing problem",
      "causeType": "product",
      "evidenceSummary": "78% of churned accounts had not logged in for 14+ days pre-churn. Users who don't use the product can't perceive its value. Reporting adoption -52pp vs retained — the highest-value features are precisely the ones churned users aren't reaching.",
      "compoundingFactors": [
        "Team adoption failure prevents users from reaching collaboration features that justify subscription pricing over free alternatives"
      ]
    },
    {
      "signal": "Missing integrations — Slack gap",
      "symptom": "Users leave because the product doesn't connect to their existing Slack workflows",
      "rootCause": "Product integration roadmap has not prioritised the workflow tool used by the majority of the target market — forcing users to choose between their communication hub and the product",
      "causeType": "product",
      "evidenceSummary": "24 exit responses is the third-highest exit reason and represents a hard blocker — unlike soft preference signals, missing integrations are binary. Users either have the integration or they don't.",
      "compoundingFactors": [
        "Competitive displacement by Notion is partly driven by Notion's richer integration ecosystem"
      ]
    },
    {
      "signal": "Support experience failure",
      "symptom": "Users with problems wait too long for help and churn before resolution",
      "rootCause": "Support capacity and response SLA are not calibrated to the volume and urgency of pre-churn risk signals — slow support during the critical early tenure period converts recoverable problems into lost accounts",
      "causeType": "operational",
      "evidenceSummary": "31-hour first response and 4.7-day resolution for accounts with avg tenure of 4.2 months means most churned accounts hit a problem, waited nearly a week for resolution, and left before it was fixed. 29% of tickets were bug reports — these users had genuine blockers, not questions.",
      "compoundingFactors": [
        "How-to tickets (44%) signal onboarding gaps that support is absorbing — fixing onboarding reduces support volume and frees capacity for urgent issues"
      ]
    },
    {
      "signal": "Competitive displacement — Notion",
      "symptom": "Users explicitly switch to Notion",
      "rootCause": "Product is losing the simplicity and all-in-one positioning battle in the SMB segment — Notion's lower friction and broader integration ecosystem makes it a credible default for teams that want one tool",
      "causeType": "commercial",
      "evidenceSummary": "19 explicit Notion mentions in exit surveys. Notion competes on exactly the weaknesses in the churn data — simplicity (vs adoption friction) and integrations (vs missing Slack).",
      "compoundingFactors": [
        "Missing Slack integration makes the competitive gap wider",
        "Onboarding friction makes Notion's simpler setup more attractive to comparison shoppers"
      ]
    }
  ],
  "primaryFailureMode": "The product fails to activate teams into collaborative workflows during the critical first 90 days — users create tasks solo, never reach the features that deliver team value, perceive the price as unjustified, and churn before becoming sticky",
  "secondaryFailureMode": "Operational support is too slow to rescue at-risk accounts during early tenure — by the time tickets are resolved, the user has already decided to leave"
}
```

---



### Step 3 of 4 - Prioritise Interventions

```markdown
## Step 3 of 4 — Prioritise Interventions

### What this step does
Take the root cause diagnosis and generate a prioritised set of interventions
ranked by impact/effort ratio and time to measurable result.

### Input from Step 2
\```json
[paste Step 2 output here]
\```

### Your task
Design 4–6 specific interventions that address the diagnosed root causes.
Rank them by the combination of MRR saved, implementation effort, and
speed to measurable result. For each, specify which root cause it addresses,
what success looks like, and how long until the impact is measurable.

Do not write implementation details yet — this step is prioritisation only.

### Output format
Return a JSON object only — no explanation.

\```typescript
{
  interventions: {
    rank: number
    title: string
    addressesRootCause: string[]   // root cause category names from Step 2
    mrrAtRisk: number              // from Step 1 signal data
    effort: "low" | "medium" | "high"
    timeToMeasurable: string       // e.g. "2–3 weeks", "1 quarter"
    successMetric: string          // specific measurable outcome
    owner: string                  // team or role
  }[]
  quickWins: string[]              // interventions deliverable in under 2 weeks
  strategicBets: string[]         // high-effort, high-impact interventions
  doNotPrioritise: string[]       // things that seem obvious but shouldn't come first
}
\```

### Rules
- Rank 1 is highest priority
- doNotPrioritise must explain why each item should wait — not just list it
- Return raw JSON only
```

**Step 3 Output:**

```json
{
  "interventions": [
    {
      "rank": 1,
      "title": "Team activation milestone in onboarding — force a collaborative moment in the first 7 days",
      "addressesRootCause": ["Low team adoption — product never became a habit", "Perceived value gap — price vs utility"],
      "mrrAtRisk": 17608,
      "effort": "medium",
      "timeToMeasurable": "3–4 weeks",
      "successMetric": "Week-1 retention increases by 5pp and comments feature adoption increases by 10pp within 30 days of launch",
      "owner": "Product"
    },
    {
      "rank": 2,
      "title": "Support SLA reduction for accounts under 90 days tenure — first response under 4 hours",
      "addressesRootCause": ["Support experience failure"],
      "mrrAtRisk": 6248,
      "effort": "low",
      "timeToMeasurable": "2–3 weeks",
      "successMetric": "Churned account ticket resolution time drops below 24 hours; early-tenure churn rate decreases by 15% within 60 days",
      "owner": "Customer Success"
    },
    {
      "rank": 3,
      "title": "Slack integration — native two-way task notifications",
      "addressesRootCause": ["Missing integrations — Slack gap", "Competitive displacement — Notion"],
      "mrrAtRisk": 8520,
      "effort": "high",
      "timeToMeasurable": "1 quarter post-launch",
      "successMetric": "Slack integration adoption reaches 20% of active accounts within 60 days; Notion-citing exit survey responses decrease by 30%",
      "owner": "Engineering"
    },
    {
      "rank": 4,
      "title": "In-app feature discovery — contextual prompts surfacing reporting and API for power users",
      "addressesRootCause": ["Perceived value gap — price vs utility", "Low team adoption — product never became a habit"],
      "mrrAtRisk": 9656,
      "effort": "low",
      "timeToMeasurable": "2–3 weeks",
      "successMetric": "Reporting feature adoption increases by 15pp among accounts in months 2–4 of tenure",
      "owner": "Product"
    },
    {
      "rank": 5,
      "title": "Churn risk early warning system — flag accounts with DAU below 1.5 and no comments activity at day 21",
      "addressesRootCause": ["Low team adoption — product never became a habit", "Support experience failure"],
      "mrrAtRisk": 9656,
      "effort": "medium",
      "timeToMeasurable": "4–6 weeks to instrument; 8–10 weeks to show retention impact",
      "successMetric": "80% of flagged at-risk accounts receive a CS touchpoint within 48 hours; flagged account 90-day churn rate decreases by 20%",
      "owner": "Customer Success + Engineering"
    }
  ],
  "quickWins": [
    "Support SLA reduction for early-tenure accounts — policy change, no engineering required",
    "In-app feature discovery prompts — low engineering effort, measurable within weeks"
  ],
  "strategicBets": [
    "Slack integration — high effort but addresses the third-largest exit reason and directly counters Notion displacement"
  ],
  "doNotPrioritise": [
    "Pricing changes — the value gap is a realisation problem, not a price problem; reducing price without fixing adoption will compress margin without reducing churn",
    "Broad marketing to replace churned accounts — acquiring new users into a broken onboarding flow accelerates MRR loss, not growth"
  ]
}
```

---



### Step 4 of 4 - Write the Churn Reduction Playbook

```markdown
## Step 4 of 4 — Write the Churn Reduction Playbook

### What this step does
Synthesise the signal collection, root cause diagnosis, and prioritised
interventions into a complete, executive-ready churn reduction playbook.

### Input from Steps 1–3
\```json
[paste Step 1 output]
[paste Step 2 output]
[paste Step 3 output]
\```

### Your task
Write a churn reduction playbook in markdown. It should be detailed enough
for a leadership team to align on priorities and for individual teams to
begin execution immediately — without needing to revisit the underlying
analysis.

### Output format
A markdown document with these exact sections:
1. Situation Summary (3–4 sentences — the problem in plain language)
2. What the Data Tells Us (key findings from Steps 1 and 2)
3. Root Cause Diagnosis (primary and secondary failure modes)
4. Intervention Roadmap (ranked table with owner, effort, timeline)
5. Quick Wins — Start This Week (Step 3 quick wins with specific actions)
6. Success Metrics (how we'll know it's working — 30/60/90 day targets)
7. What Not to Do (from Step 3 doNotPrioritise — with reasoning)

### Rules
- Write for a mixed audience — technical enough for product and engineering,
  accessible enough for a CEO or board member
- Every recommendation must trace back to a specific data point
- Avoid hedging language — state findings and recommendations directly
```



## Variations


| Pipeline                     | Stages                                                                                            |
| ---------------------------- | ------------------------------------------------------------------------------------------------- |
| Competitive landscape report | Collect competitor data → Map feature gaps → Assess threat levels → Strategic response            |
| Quarterly business review    | Normalise metrics → Diagnose trends → Prioritise focus areas → Write QBR narrative                |
| User research synthesis      | Collect interview themes → Cluster insights → Rank by frequency/impact → Write research report    |
| Content performance audit    | Collect channel data → Diagnose underperformers → Prioritise content types → Build editorial plan |
| Onboarding funnel analysis   | Map drop-off points → Diagnose friction causes → Design interventions → Write optimisation spec   |
| NPS driver analysis          | Segment promoters/detractors → Extract themes → Diagnose gaps → Write improvement roadmap         |




## Tips

**Four steps is the sweet spot for analysis chains.**
Collect → Diagnose → Prioritise → Prescribe maps to how experienced
analysts actually work. Fewer steps compress stages that need separation.
More steps create handoff overhead without meaningful quality gains.

**Step 1 should always be normalisation, not interpretation.**
The most common chaining mistake is asking Step 1 to both collect
and interpret. Normalisation is a mechanical task — grouping, deduplicating,
weighting. Interpretation is a reasoning task. Mixing them produces
output where the groupings are already biased by early conclusions.

**"Do not recommend solutions yet" is the most important constraint.** Add this to every step that precedes your recommendation step. Without it, the model jumps ahead producing shallow diagnosis because it has already committed to a solution.

**The** `doNotPrioritise` **field prevents the obvious wrong answers.**
Every churn analysis produces a temptation to cut price or run a win-back
campaign. Building explicit "do not do this" reasoning into Step 3 means
the playbook defends against those decisions before someone suggests them.

**Chain length should match stakeholder count.**
If the output goes to one team, a 3-step chain is enough. If it goes to
leadership, product, engineering, and CS separately, invest in a 4–5 step
chain that produces a synthesis step tailored to each audience.

## Related Prompts

- [role-prompting.md](./role-prompting.md) - assign a senior analyst persona at Step 1 to frame the entire chain
- [chain-of-thought.md](./chain-of-thought.md) - use CoT within diagnosis steps for rigorous hypothesis elimination
- [structured-output.md](./structured-output.md) - enforce typed JSON at every intermediate step for reliable handoffs
- [few-shot.md](./few-shot.md) - demonstrate expected output format for steps with complex or opinionated structure



## Tags

`prompt-chaining` `churn-analysis` `root-cause` `pipelines` `multi-step` `business-intelligence` `analysis` `prompt-patterns`