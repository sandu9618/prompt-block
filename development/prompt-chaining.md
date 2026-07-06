# Prompt Chaining — Development

## Description

Breaks a complex development task into a sequence of focused prompts where
the output of each step becomes the input of the next. Instead of asking
the model to do everything at once in a single giant prompt, you decompose
the work into stages — each one small enough to do well.

Prompt chaining trades the convenience of one prompt for the reliability of
many. Each step has a single responsibility, a clear input, and a verifiable
output. Errors surface early and stay contained. The final result is more
consistent, more accurate, and easier to debug than anything a single
monolithic prompt produces.

## Usage

Identify the natural stages of your task — where does understanding need to
happen before building can start? Where does analysis need to finish before
a decision can be made? Each stage boundary is a chain link.

Design each prompt to consume the previous step's output explicitly. Pass
structured data between steps where possible — JSON output from Step 2 becomes
the typed input to Step 3. This makes the pipeline debuggable: you can inspect
and correct the output at any stage before it propagates forward.

Use this pattern for: feature development pipelines, automated code review
systems, multi-stage refactoring, codebase onboarding, release note generation,
bug triage workflows, and any task complex enough that a single prompt produces
inconsistent or incomplete results.

## Prompt

```markdown
## Step [N] of [TOTAL] — [STEP NAME]

### What this step does
[One sentence describing the single responsibility of this step.]

### Input from previous step
[Paste the output of the previous step here — or describe the starting
input if this is Step 1.]

\```[format]
[Previous step output or initial input]
\```

### Your task
[Clear, focused instruction for this step only. Do not ask it to do
anything that belongs in a later step.]

### Output format
[Describe exactly what this step should return — JSON schema, markdown
structure, code block, or plain list. Be explicit so the next step
can consume it without ambiguity.]

### Rules
- Focus only on this step — do not perform analysis or transformations
  that belong to a later stage
- [Any constraints specific to this step]
- Return [FORMAT] only — no explanation unless explicitly requested
```

## Example — Full 4-Step Chain

A pipeline that takes a raw GitHub issue and produces a ready-to-implement
technical specification, broken into: understand → analyse → design → specify.

---

### Step 1 of 4 — Understand the Issue

```markdown
## Step 1 of 4 — Understand the Issue

### What this step does
Extract structured information from a raw GitHub issue into a clean,
unambiguous problem statement.

### Input
\```markdown
**Title:** API response times degrading under load — users reporting timeouts

**Body:**
Hey team, we've been getting reports from enterprise customers that the
/api/v2/reports endpoint starts timing out when more than ~200 users are
active simultaneously. It seems to get worse throughout the day. Our SLA
is 2s response time but we're seeing p99 hit 8–12s during peak hours.
We're on PostgreSQL + Node.js. I checked and there's no caching on this
endpoint at all. Might be related to the new aggregation queries we added
in v2.3? Not sure if it's a DB issue or something in our application layer.
cc @backend-team

**Labels:** performance, bug, enterprise, p1
**Reporter:** enterprise customer via support ticket
\```

### Your task
Extract the core problem into a structured summary. Identify: what is
broken, who is affected, what the measurable impact is, and what
hypotheses already exist. Do not propose solutions yet.

### Output format
Return a JSON object only — no explanation.

\```typescript
{
  title: string           // concise problem title
  affectedEndpoint: string
  symptom: string         // observable behaviour that is wrong
  impact: {
    who: string           // who is affected
    severity: "p0" | "p1" | "p2" | "p3"
    sla: string           // the SLA being violated
    measuredValue: string // actual observed value
  }
  hypotheses: string[]    // existing theories from the issue, verbatim intent
  unknowns: string[]      // what is explicitly unclear or unconfirmed
  stack: string[]         // technologies mentioned
}
\```

### Rules
- Extract only what is stated or clearly implied — do not invent hypotheses
- Return raw JSON only — no markdown fences, no preamble
```

**Step 1 Output:**
```json
{
  "title": "GET /api/v2/reports response time degrades to 8–12s p99 under concurrent load",
  "affectedEndpoint": "GET /api/v2/reports",
  "symptom": "Response times degrade from normal to 8–12s p99 when ~200+ users are active simultaneously, worsening throughout the day",
  "impact": {
    "who": "Enterprise customers",
    "severity": "p1",
    "sla": "2s response time",
    "measuredValue": "8–12s p99 during peak hours"
  },
  "hypotheses": [
    "Aggregation queries added in v2.3 may be contributing",
    "No caching on this endpoint — adding caching might help",
    "Could be database layer or application layer"
  ],
  "unknowns": [
    "Whether the issue is in the database layer or application layer",
    "Whether v2.3 aggregation queries are directly correlated",
    "Current query execution plans and index coverage"
  ],
  "stack": ["PostgreSQL", "Node.js"]
}
```

---

### Step 2 of 4 — Analyse Root Causes

```markdown
## Step 2 of 4 — Analyse Root Causes

### What this step does
Take the structured problem summary and systematically identify the most
likely root causes, ranked by probability and impact.

### Input from Step 1
\```json
{
  "title": "GET /api/v2/reports response time degrades to 8–12s p99 under concurrent load",
  "affectedEndpoint": "GET /api/v2/reports",
  "symptom": "Response times degrade from normal to 8–12s p99 when ~200+ users are active simultaneously, worsening throughout the day",
  "impact": {
    "who": "Enterprise customers",
    "severity": "p1",
    "sla": "2s response time",
    "measuredValue": "8–12s p99 during peak hours"
  },
  "hypotheses": [
    "Aggregation queries added in v2.3 may be contributing",
    "No caching on this endpoint — adding caching might help",
    "Could be database layer or application layer"
  ],
  "unknowns": [
    "Whether the issue is in the database layer or application layer",
    "Whether v2.3 aggregation queries are directly correlated",
    "Current query execution plans and index coverage"
  ],
  "stack": ["PostgreSQL", "Node.js"]
}
\```

### Your task
Identify the 3–5 most likely root causes for this performance degradation
pattern. For each, explain the mechanism, the evidence from the issue that
supports it, and what investigation would confirm or rule it out.
Rank them by likelihood given the available information.

### Output format
Return a JSON array only — no explanation.

\```typescript
{
  rootCauses: {
    rank: number
    cause: string
    mechanism: string       // how this cause produces the observed symptom
    supportingEvidence: string[]
    investigationStep: string  // single query, metric, or check to confirm/rule out
    likelihood: "high" | "medium" | "low"
  }[]
}
\```

### Rules
- Rank 1 is the most likely cause
- Base likelihood on the pattern of symptoms — worsens throughout the day
  and under concurrency — not just the hypotheses in the issue
- Return raw JSON only
```

**Step 2 Output:**
```json
{
  "rootCauses": [
    {
      "rank": 1,
      "cause": "Missing or insufficient indexes on aggregation query columns",
      "mechanism": "Aggregation queries added in v2.3 perform GROUP BY or JOIN operations on unindexed columns, causing sequential scans. Under concurrent load, multiple simultaneous sequential scans contend for I/O and shared buffers, compounding latency.",
      "supportingEvidence": [
        "Degradation correlates with v2.3 aggregation query addition",
        "Worsens under concurrency — sequential scans block each other",
        "No mention of indexes being added alongside v2.3 queries"
      ],
      "investigationStep": "Run EXPLAIN ANALYZE on the /reports query and check for Seq Scan nodes on large tables",
      "likelihood": "high"
    },
    {
      "rank": 2,
      "cause": "Connection pool exhaustion under concurrent load",
      "mechanism": "Each concurrent request holds a DB connection for the duration of a slow aggregation query. With 200 users, the pool fills and new requests queue — causing cascading latency that worsens throughout the day as connections are not returned promptly.",
      "supportingEvidence": [
        "Symptom threshold (~200 concurrent users) aligns with typical default pool sizes of 10–20",
        "Worsens throughout the day — consistent with slow connection leak or pool not recovering"
      ],
      "investigationStep": "Check pg pool metrics — active connections, idle connections, and queue depth during peak hours",
      "likelihood": "high"
    },
    {
      "rank": 3,
      "cause": "N+1 query pattern in the aggregation logic",
      "mechanism": "The v2.3 aggregation feature may execute one query per report row to fetch related data, turning a single endpoint call into hundreds of database round trips under load.",
      "supportingEvidence": [
        "New aggregation queries in v2.3 not previously performance-tested under load",
        "Latency scales with concurrency — consistent with multiplying query counts"
      ],
      "investigationStep": "Log query count per request to /reports — more than 5–10 queries per call indicates N+1",
      "likelihood": "medium"
    },
    {
      "rank": 4,
      "cause": "Autovacuum or table bloat degrading query performance over the day",
      "mechanism": "Without vacuuming, dead tuples accumulate in active tables, increasing sequential scan time. A high write rate on the reports table throughout the day would cause progressive degradation by end of business.",
      "supportingEvidence": [
        "Worsens throughout the day — consistent with intra-day table bloat accumulation"
      ],
      "investigationStep": "Check pg_stat_user_tables for n_dead_tup on the reports table and last_autovacuum timestamp",
      "likelihood": "low"
    }
  ]
}
```

---

### Step 3 of 4 — Design the Solution

```markdown
## Step 3 of 4 — Design the Solution

### What this step does
Take the ranked root cause analysis and design a concrete, prioritised
remediation plan.

### Input from Step 2
\```json
[paste Step 2 output here]
\```

### Your task
Design a solution that addresses the top root causes in priority order.
For each fix, provide the implementation approach, estimated effort,
and the risk of the change. Then define the success criteria — how will
we know the fix worked?

### Output format
Return a JSON object only — no explanation.

\```typescript
{
  fixes: {
    priority: number
    title: string
    addressesCause: number[]    // rank numbers from root cause analysis
    approach: string            // how to implement this fix
    effortEstimate: "hours" | "days" | "weeks"
    deploymentRisk: "low" | "medium" | "high"
    canDeployWithoutDowntime: boolean
  }[]
  successCriteria: {
    metric: string
    target: string
    measureWith: string
  }[]
  recommendedOrder: string     // one sentence on sequencing rationale
}
\```

### Rules
- Order fixes by impact/effort ratio — highest impact, lowest effort first
- Flag any fix that requires a migration or downtime explicitly
- Return raw JSON only
```

**Step 3 Output:**
```json
{
  "fixes": [
    {
      "priority": 1,
      "title": "Add indexes on aggregation query columns",
      "addressesCause": [1],
      "approach": "Run EXPLAIN ANALYZE to identify sequential scans. Add CONCURRENTLY indexes on GROUP BY and JOIN columns. For composite queries, add covering indexes that include all selected columns.",
      "effortEstimate": "hours",
      "deploymentRisk": "low",
      "canDeployWithoutDowntime": true
    },
    {
      "priority": 2,
      "title": "Increase and tune connection pool size",
      "addressesCause": [2],
      "approach": "Set pg pool max to (2 × CPU cores + effective_spindle_count). Add pool monitoring with pg-monitor. Set statement_timeout to 5s to prevent long-running queries from holding connections.",
      "effortEstimate": "hours",
      "deploymentRisk": "low",
      "canDeployWithoutDowntime": true
    },
    {
      "priority": 3,
      "title": "Add response caching for report aggregations",
      "addressesCause": [1, 2, 3],
      "approach": "Cache GET /reports responses in Redis with a 60s TTL keyed by user ID + query params. Invalidate on write events to the reports table. This reduces DB load regardless of root cause.",
      "effortEstimate": "days",
      "deploymentRisk": "medium",
      "canDeployWithoutDowntime": true
    },
    {
      "priority": 4,
      "title": "Audit and fix N+1 queries in aggregation logic",
      "addressesCause": [3],
      "approach": "Instrument query count per request with a middleware logger. Replace identified N+1 patterns with JOIN queries or DataLoader batching.",
      "effortEstimate": "days",
      "deploymentRisk": "medium",
      "canDeployWithoutDowntime": true
    }
  ],
  "successCriteria": [
    {
      "metric": "p99 response time for GET /api/v2/reports",
      "target": "Under 2s at 200 concurrent users",
      "measureWith": "Load test with k6 simulating 200 VUs over 10 minutes"
    },
    {
      "metric": "Database connection pool utilisation",
      "target": "Below 70% at peak load",
      "measureWith": "pg-monitor dashboard during peak hours"
    },
    {
      "metric": "Query count per request",
      "target": "Under 10 queries per /reports call",
      "measureWith": "Request-scoped query counter middleware"
    }
  ],
  "recommendedOrder": "Apply index fixes first — they are zero-risk, deploy in minutes, and will confirm or rule out root cause 1 before investing in caching infrastructure."
}
```

---

### Step 4 of 4 — Write the Technical Specification

```markdown
## Step 4 of 4 — Write the Technical Specification

### What this step does
Synthesise the root cause analysis and solution design into a complete,
implementation-ready technical specification a developer can pick up and act on.

### Input from Steps 1–3
\```json
[paste Step 1 output]
[paste Step 2 output]
[paste Step 3 output]
\```

### Your task
Write a technical specification document in markdown. It should be
detailed enough that a mid-level engineer with no prior context can
understand the problem, implement the fixes, and verify success —
without needing to ask clarifying questions.

### Output format
A markdown document with these exact sections:
1. Problem Summary
2. Root Cause Analysis
3. Implementation Plan (ordered fixes with code examples)
4. Testing & Verification
5. Rollback Plan

### Rules
- Include concrete code examples for each fix
- Every fix must reference the success criteria from Step 3
- Keep language direct — this is a spec, not a blog post
```

## Variations

| Pipeline | Stages |
|---|---|
| Feature development | Requirements → Design → Implementation plan → Test cases |
| Automated PR review | Summarise changes → Identify risks → Suggest improvements → Write PR description |
| Codebase onboarding | Map structure → Identify entry points → Document data flow → Generate questions |
| Bug triage | Reproduce steps → Isolate cause → Design fix → Write regression test |
| Database migration | Audit current schema → Identify risks → Generate migration SQL → Write rollback |
| API design | Define resources → Design endpoints → Generate OpenAPI spec → Write usage examples |
| Dependency upgrade | Audit breaking changes → Identify affected code → Generate migration guide → Update tests |

## Tips

**Each step should have one job.**
If you find yourself writing "and also..." in a step's task, split it.
A step that analyses AND designs AND writes code will do all three poorly.
A step that only analyses will do it well.

**Pass structured output between steps, not prose.**
JSON output from Step 2 as input to Step 3 means the next step gets
reliable, parseable data — not a paragraph it has to re-interpret.
Use the Structured Output pattern for every intermediate step.

**Verify each step before continuing.**
The power of chaining is that you can inspect and correct at each stage.
If Step 2's root cause analysis misses something, fix it before running
Step 3 — don't let a wrong assumption compound through four steps.

**Name your steps clearly.**
`"Step 2 of 4 — Analyse Root Causes"` tells the model exactly where it
is in the pipeline and prevents it from jumping ahead to solutions.
The model performs better when it knows its scope is bounded.

**Start with 2–3 steps, not 6.**
Design the minimum chain that produces a useful result. Add steps only
when you find a specific stage producing inconsistent output. Complexity
has a cost — every extra step is another place for errors to accumulate.

**Chaining compounds the other four patterns.**
Use Role Prompting to set the expert persona at Step 1.
Use Chain of Thought for reasoning-heavy steps.
Use Few-Shot to demonstrate the expected output format for a step.
Use Structured Output for every intermediate step that feeds the next.

## Related Prompts

- [role-prompting.md](./role-prompting.md) — set an expert persona at the start of the chain
- [chain-of-thought.md](./chain-of-thought.md) — use CoT for reasoning-heavy steps in the pipeline
- [structured-output.md](./structured-output.md) — enforce typed JSON between every step for reliable handoffs
- [few-shot.md](./few-shot.md) — demonstrate expected output format for steps with complex structure

## Tags

`prompt-chaining` `pipelines` `multi-step` `automation` `architecture` `debugging` `development` `prompt-patterns`