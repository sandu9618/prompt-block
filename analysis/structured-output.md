# Structured Output — Analysis

## Description

Instructs the AI to return analytical findings in a precise, machine-readable
format instead of narrative prose. Rather than a written report you read once
and file away, you get clean JSON that feeds directly into dashboards,
alerting systems, automated reports, or downstream processing pipelines.

In analysis, structured output closes the gap between insight generation and
insight delivery. A finding locked inside a paragraph helps one person who
reads it. The same finding in a typed JSON schema can trigger a Slack alert,
populate a Notion database, feed a BI dashboard, or kick off a follow-up
workflow automatically.

## Usage

Define the exact output schema upfront — every field name, its type, its
allowed values, and what it represents. Instruct the model explicitly to
return only the JSON with no prose, no markdown fences, and no explanation.

Use this pattern for: automated metric commentary, recurring report generation,
user feedback processing pipelines, anomaly detection outputs, competitive
intelligence extraction, multi-source data synthesis, and any analysis that
needs to be consumed by code rather than read by a human.

## Prompt

```markdown
Analyse the following [INPUT TYPE] and return a structured JSON response.

## Input

[Paste your metrics, feedback, logs, research, or raw data here.]

## Context

- **Source**: [Where this data comes from]
- **Time period**: [Date range or snapshot date]
- **Baseline**: [Normal values for comparison — or "none" if first analysis]
- **Purpose**: [What decision this analysis will inform]

## Output Schema

Return ONLY a valid JSON object matching this exact schema.
No explanation, no markdown fences, no text before or after the JSON.

\```typescript
{
  [field: string]: [type]         // [what this field captures]
  [field: string]: [type][]       // [array field description]
  [field: string]: {
    [nestedField: string]: [type] // [nested object description]
  }
}
\```

## Rules

- Return raw JSON only — no \`\`\`json fences, no preamble, no explanation
- If a field has no applicable value, use null — never omit required fields
- [Field name] must be one of: [ENUM VALUES]
- All string values must be written as complete sentences or phrases —
  no abbreviations or single words unless the field is a tag or enum
```

## Example Input

```markdown
Analyse the following weekly product metrics and return a structured
executive summary suitable for a Friday board update.

## Input

\```
Week: 14 June – 20 June 2025

Acquisition:
  New signups:          1,847  (prev week: 1,612  | +14.6%)
  Trial activations:    1,203  (prev week: 1,089  | +10.5%)
  Activation rate:      65.1%  (prev week: 67.6%  | -2.5pp)

Engagement:
  DAU:                  4,921  (prev week: 4,701  | +4.7%)
  DAU/MAU:              18.3%  (prev week: 17.9%  | +0.4pp)
  Avg session length:   11.2m  (prev week: 12.8m  | -12.5%)
  Feature: AI summary:  29%    of DAU (prev week: 24% | +5pp)

Retention:
  Week-1 retention:     61%    (prev week: 63%    | -2pp)
  Week-4 retention:     38%    (prev week: 38%    | flat)
  Churned accounts:     43     (prev week: 31     | +38.7%)

Revenue:
  MRR:                  $184,200 (prev week: $179,400 | +2.7%)
  New MRR:              $9,800   (prev week: $7,200   | +36.1%)
  Churned MRR:          $4,100   (prev week: $2,300   | +78.3%)
  Net new MRR:          $5,700   (prev week: $4,900   | +16.3%)
  ARPU:                 $37.40   (prev week: $36.10   | +3.6%)
\```

## Context

- **Source**: Internal analytics dashboard + Stripe
- **Time period**: Week of 14–20 June 2025
- **Baseline**: Prior week values shown inline above
- **Purpose**: Friday board update — executives need signal, not raw data

## Output Schema

Return ONLY a valid JSON object matching this exact schema.
No explanation, no markdown fences, no text before or after the JSON.

\```typescript
{
  weekEnding: string               // ISO date string e.g. "2025-06-20"
  overallHealth: "strong" | "healthy" | "mixed" | "concerning" | "critical"
  headline: string                 // one sentence board-level summary of the week
  highlights: {
    metric: string                 // name of the metric
    value: string                  // formatted current value
    change: string                 // formatted change vs prior week
    signal: "positive" | "negative" | "neutral"
    implication: string            // one sentence business meaning
  }[]
  concerns: {
    area: string                   // product area or metric group
    observation: string            // what the data shows
    severity: "low" | "medium" | "high" | "critical"
    recommendedAction: string      // specific next step
    owner: string | null           // team or role responsible — null if unclear
  }[]
  metricsSnapshot: {
    acquisition: {
      newSignups: number
      trialActivations: number
      activationRate: number       // as decimal e.g. 0.651
      weekOnWeekChange: number     // signups % change as decimal
    }
    engagement: {
      dau: number
      dauMauRatio: number
      avgSessionMinutes: number
      topFeatureAdoptionPct: number
      topFeatureName: string
    }
    retention: {
      week1: number                // as decimal
      week4: number                // as decimal
      churnedAccounts: number
    }
    revenue: {
      mrr: number
      newMrr: number
      churnedMrr: number
      netNewMrr: number
      arpu: number
    }
  }
  watchlistNextWeek: string[]      // metrics or areas to monitor closely next week
}
\```

## Rules

- Return raw JSON only — no \`\`\`json fences, no preamble, no explanation
- overallHealth must be exactly one of: "strong", "healthy", "mixed", "concerning", "critical"
- signal must be exactly one of: "positive", "negative", "neutral"
- severity must be exactly one of: "low", "medium", "high", "critical"
- highlights must contain 3–5 items — the most significant signals only
- concerns must contain only genuine concerns — omit this array entry if
  there is nothing actionable to flag for a given area
- All implication and recommendedAction strings must be complete sentences
```

## Example Output

```json
{
  "weekEnding": "2025-06-20",
  "overallHealth": "mixed",
  "headline": "Strong acquisition and revenue growth this week is offset by rising churn, falling session engagement, and a week-1 retention dip that warrants close monitoring.",
  "highlights": [
    {
      "metric": "New MRR",
      "value": "$9,800",
      "change": "+36.1% week-on-week",
      "signal": "positive",
      "implication": "New MRR growth significantly outpaced prior weeks, suggesting the recent pricing page update or sales activity is converting at higher rates."
    },
    {
      "metric": "Churned MRR",
      "value": "$4,100",
      "change": "+78.3% week-on-week",
      "signal": "negative",
      "implication": "Churned MRR nearly doubled in one week — at this rate it will erode the new MRR gains within three weeks and requires immediate investigation into which account segments are leaving."
    },
    {
      "metric": "AI Summary feature adoption",
      "value": "29% of DAU",
      "change": "+5pp week-on-week",
      "signal": "positive",
      "implication": "AI Summary adoption continues to accelerate and is now the most-used non-core feature, suggesting it is becoming a retention driver worth prioritising in onboarding."
    },
    {
      "metric": "Average session length",
      "value": "11.2 minutes",
      "change": "-12.5% week-on-week",
      "signal": "negative",
      "implication": "A 12.5% drop in session length in one week is a meaningful engagement decline — combined with the week-1 retention dip, this may indicate new cohorts are not finding value as quickly as prior cohorts."
    },
    {
      "metric": "New signups",
      "value": "1,847",
      "change": "+14.6% week-on-week",
      "signal": "positive",
      "implication": "Signup volume reached its highest weekly total this quarter, likely driven by the content campaign launched on 10 June."
    }
  ],
  "concerns": [
    {
      "area": "Churn",
      "observation": "43 accounts churned this week versus 31 last week — a 38.7% increase — and churned MRR of $4,100 is the highest recorded this quarter.",
      "severity": "high",
      "recommendedAction": "Pull the churned account list and segment by plan type, tenure, and last active date to identify whether this is concentrated in a specific cohort or distributed — the pattern will determine whether this is a product or a commercial issue.",
      "owner": "Customer Success"
    },
    {
      "area": "Week-1 retention",
      "observation": "Week-1 retention dropped 2 percentage points to 61% — a small but consistent directional decline over three consecutive weeks.",
      "severity": "medium",
      "recommendedAction": "Compare activation event completion rates for this week's signup cohort against the prior four-week average to determine whether the onboarding flow is underperforming for the higher signup volume.",
      "owner": "Product"
    },
    {
      "area": "Trial activation rate",
      "observation": "Despite 14.6% more signups, the activation rate fell from 67.6% to 65.1% — meaning a larger share of new signups are not reaching the activation milestone.",
      "severity": "medium",
      "recommendedAction": "Check whether the signup volume increase came from a lower-intent channel and segment activation rate by acquisition source before assuming an onboarding problem.",
      "owner": "Growth"
    }
  ],
  "metricsSnapshot": {
    "acquisition": {
      "newSignups": 1847,
      "trialActivations": 1203,
      "activationRate": 0.651,
      "weekOnWeekChange": 0.146
    },
    "engagement": {
      "dau": 4921,
      "dauMauRatio": 0.183,
      "avgSessionMinutes": 11.2,
      "topFeatureAdoptionPct": 0.29,
      "topFeatureName": "AI Summary"
    },
    "retention": {
      "week1": 0.61,
      "week4": 0.38,
      "churnedAccounts": 43
    },
    "revenue": {
      "mrr": 184200,
      "newMrr": 9800,
      "churnedMrr": 4100,
      "netNewMrr": 5700,
      "arpu": 37.40
    }
  },
  "watchlistNextWeek": [
    "Churned MRR — monitor whether the spike continues or reverts; two consecutive high-churn weeks would indicate a structural problem",
    "Week-1 retention — third consecutive weekly decline; a fourth would confirm a degrading onboarding experience for the new signup cohort",
    "AI Summary adoption — if it crosses 35% of DAU it becomes a primary retention driver worth surfacing in the upgrade flow",
    "Session length — determine whether the drop is cohort-specific (new low-intent signups diluting the average) or product-wide"
  ]
}
```

## Variations

| Scenario | Schema to define |
|---|---|
| User feedback batch processing | `{ responseId, sentiment, coreIssue, productArea, priority, tags[] }` |
| Competitive intelligence extraction | `{ competitor, feature, maturity, ourGap, threatLevel, sourceUrl }` |
| Survey results synthesis | `{ theme, frequency, sentiment, representativeQuotes[], recommendation }` |
| Cohort retention analysis | `{ cohort, week1, week4, week12, dropOffStage, likelyCause }` |
| Error log triage | `{ errorCode, frequency, affectedUsers, firstSeen, likelyCause, severity }` |
| Content performance analysis | `{ title, channel, impressions, ctr, conversionRate, topInsight, action }` |
| Sales pipeline analysis | `{ stage, dealCount, avgValue, conversionRate, bottleneck, recommendation }` |

## Tips

**Define the `overallHealth` enum before anything else.**
A single categorical field that summarises the entire analysis forces the
model to make a global judgement first. This anchors the rest of the output —
a "critical" health score produces more severe concern ratings than a
"healthy" one. It also gives the consumer of your JSON an instant filter
before parsing the detail.

**Separate raw metrics from interpreted fields.**
The `metricsSnapshot` object in the schema above holds raw numbers.
The `highlights` and `concerns` arrays hold interpretation. Keeping
these separate means you can pass raw metrics to a chart and interpreted
findings to a Slack message from the same JSON response.

**`recommendedAction` must be a complete sentence with a verb.**
`"Investigate churn"` is not an action. `"Pull the churned account list
and segment by plan type to identify whether this is concentrated in
a specific cohort"` is an action. Enforce sentence structure in your rules.

**The `watchlistNextWeek` array creates continuity.**
For recurring analyses, always include a forward-looking field that flags
what to monitor next cycle. This makes each week's output the starting
context for the next run — turning a one-off analysis into a living document.

**Add a `null` example to your rules.**
Explicitly say: `"If concerns contains no genuine items, return an empty
array — do not fabricate concerns."` Without this, the model will
manufacture low-severity concerns to fill the schema.

**Validate with Zod in your pipeline.**
Treat AI-generated JSON as untrusted external data. Parse defensively:

\```typescript
import { z } from 'zod';

const HealthSchema = z.enum(['strong', 'healthy', 'mixed', 'concerning', 'critical']);

const WeeklyReportSchema = z.object({
  weekEnding: z.string(),
  overallHealth: HealthSchema,
  headline: z.string(),
  highlights: z.array(z.object({
    metric: z.string(),
    value: z.string(),
    change: z.string(),
    signal: z.enum(['positive', 'negative', 'neutral']),
    implication: z.string(),
  })).min(3).max(5),
  concerns: z.array(z.object({
    area: z.string(),
    observation: z.string(),
    severity: z.enum(['low', 'medium', 'high', 'critical']),
    recommendedAction: z.string(),
    owner: z.string().nullable(),
  })),
  watchlistNextWeek: z.array(z.string()),
});

const report = WeeklyReportSchema.parse(JSON.parse(aiResponse));
\```

## Related Prompts

- [role-prompting.md](./role-prompting.md) — prepend a data analyst persona to sharpen the interpretation before structuring
- [chain-of-thought.md](./chain-of-thought.md) — use CoT for the reasoning step, then structured output for the final formatted result
- [prompt-chaining.md](./prompt-chaining.md) — feed structured output directly as typed input to a downstream reporting or alerting pipeline

## Tags

`structured-output` `json` `metrics` `reporting` `automation` `dashboards` `analysis` `prompt-patterns`