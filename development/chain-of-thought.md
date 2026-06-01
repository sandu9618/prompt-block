# Chain of Thought — Development

## Description

Forces the AI to reason through a problem step by step before arriving at a
final answer. Instead of jumping to conclusions, the model works through the
problem systematically — evaluating options, eliminating dead ends, and
explaining its reasoning at each stage.

This dramatically improves accuracy on complex engineering decisions where
the wrong answer chosen quickly is worse than a slower, well-reasoned one.

## Usage

Present the problem clearly, then explicitly instruct the model to think
step by step before answering. Provide relevant context — constraints,
existing stack, team size, scale — so the reasoning is grounded in your
reality, not a generic scenario.

Use this pattern for: architectural decisions, debugging production issues,
technology trade-off analysis, performance bottleneck diagnosis, and any
problem where the path to the answer matters as much as the answer itself.

## Prompt

```markdown
I need you to reason through the following problem step by step.
Do not jump to a conclusion — work through each consideration before
giving a final recommendation.

---

## Problem

[Describe the problem or decision you need to reason through.]

## Context

- **Stack**: [Languages, frameworks, databases, infrastructure]
- **Scale**: [Users, requests per second, data volume]
- **Team**: [Size, skill level, relevant experience]
- **Timeline**: [Any deadline or urgency constraints]
- **Constraints**: [What you cannot change — e.g. must stay on AWS, no new dependencies]

## What I've already considered

[Optional — list approaches you've already thought about or ruled out,
so the model doesn't repeat them. Leave blank if starting fresh.]

## Reasoning steps I want you to follow

1. **Understand the problem** — restate it in your own words to confirm understanding
2. **Identify the key factors** — what criteria matter most for this decision?
3. **Evaluate each option** — go through the realistic options one by one
4. **Eliminate weak options** — explain why each option is stronger or weaker
5. **Final recommendation** — give a clear answer with the top 3 reasons why

## Constraints on your answer

- [e.g. "stay within our current AWS infrastructure"]
- [e.g. "our team has no Go experience — don't recommend Go"]
- [e.g. "we need to ship in 6 weeks — favour simplicity over perfection"]
```

## Example Input

```markdown
I need you to reason through the following problem step by step.
Do not jump to a conclusion — work through each consideration before
giving a final recommendation.

---

## Problem

We're building a real-time notification system for our SaaS app. Users need
to receive instant notifications when their teammates comment on their tasks.
I need to decide between WebSockets, Server-Sent Events (SSE), and long polling.

## Context

- **Stack**: Node.js 20, Express, React, PostgreSQL, hosted on AWS EC2
- **Scale**: ~5,000 concurrent users at peak, notifications are one-directional
  (server → client only), ~50 notifications per user per day on average
- **Team**: 3 backend engineers, comfortable with Node.js but no production
  WebSocket experience
- **Timeline**: 4 weeks to ship
- **Constraints**: No new infrastructure — must work on our existing EC2 setup.
  We're not ready to introduce Redis or a message broker yet.

## What I've already considered

- We looked at Firebase but ruled it out — we don't want vendor lock-in
- We know WebSockets are bidirectional but our use case is server → client only

## Reasoning steps I want you to follow

1. **Understand the problem** — restate it in your own words to confirm understanding
2. **Identify the key factors** — what criteria matter most for this decision?
3. **Evaluate each option** — go through WebSockets, SSE, and long polling one by one
4. **Eliminate weak options** — explain why each option is stronger or weaker
5. **Final recommendation** — give a clear answer with the top 3 reasons why

## Constraints on your answer

- Must work on existing EC2 without new infrastructure
- Favour simplicity — our team has no production WebSocket experience
- We need to ship in 4 weeks
```

## Example Output

```markdown
## Step 1 — Understanding the Problem

You need a reliable, real-time channel to push notifications from your server
to 5,000 concurrent users. The communication is strictly one-directional —
the server pushes, the client only receives. You have a tight timeline, a
Node.js stack, and a constraint against new infrastructure.

The core question is: which technology gives you reliable real-time delivery
with the least operational complexity for a team new to this problem space?

---

## Step 2 — Key Factors

Given your constraints, here's what matters most:

1. **Directionality fit** — server → client only means you don't need a
   bidirectional protocol
2. **Operational simplicity** — no production WebSocket experience means
   a simpler protocol reduces risk
3. **Infrastructure compatibility** — must work on EC2 without Redis or
   a message broker
4. **Scalability to 5,000 concurrent connections** — needs to hold under peak load
5. **Browser support** — must work across all modern browsers without polyfills
6. **Time to ship** — 4 weeks favours the option with the shortest learning curve

---

## Step 3 — Evaluating Each Option

### Option A: WebSockets

WebSockets establish a persistent, full-duplex TCP connection between client
and server. Both sides can send messages at any time.

**Strengths:**
- Lowest latency of the three options
- Well-supported in Node.js via `ws` or `socket.io`
- Handles both directions if you need it later

**Weaknesses:**
- Overkill for one-directional use — full-duplex adds complexity you don't need
- Requires careful connection state management
- Load balancers (including AWS ALB) need sticky sessions or a Redis adapter
  for multi-instance setups — which conflicts with your no-Redis constraint
- Your team has no production experience here — risk of subtle bugs under load

---

### Option B: Server-Sent Events (SSE)

SSE is an HTTP-based protocol where the server holds open a connection and
pushes text events to the client. The client uses the native `EventSource` API.

**Strengths:**
- Designed exactly for server → client streaming — fits your use case perfectly
- Built on standard HTTP — works through existing load balancers with no config
- Native browser API — no library needed on the client
- Automatic reconnection built into the spec
- Simpler to implement than WebSockets for one-directional use cases
- Works naturally with Node.js streams

**Weaknesses:**
- HTTP/1.1 has a 6-connection-per-domain browser limit — mitigated with HTTP/2
- Text-only protocol — binary data needs base64 encoding (not relevant for notifications)
- Not suitable if you ever need client → server streaming

---

### Option C: Long Polling

The client sends an HTTP request, the server holds it open until a notification
is ready (or a timeout), then responds. The client immediately sends another request.

**Strengths:**
- Works everywhere — pure HTTP, no special browser support needed
- Easiest to understand and debug
- No persistent connections to manage

**Weaknesses:**
- High server resource usage — each waiting request holds a thread/connection
- Latency spikes when the held request times out and reconnects
- At 5,000 concurrent users, 5,000 open HTTP connections with timeout/reconnect
  cycles creates significant overhead on a single EC2 instance
- More complex error handling — you need to manage timeout vs. data responses

---

## Step 4 — Eliminating Weak Options

**Long polling is eliminated first.**
At 5,000 concurrent users it creates unnecessary load — each user holds an
open connection that periodically times out and reconnects. The latency profile
is also unpredictable. There's no advantage over SSE for your use case.

**WebSockets are eliminated second.**
Your use case is server → client only — WebSockets solve a problem you don't
have. More importantly, scaling WebSockets on EC2 without Redis or sticky
sessions is a known operational trap. Given your team's experience level and
4-week timeline, this is the highest-risk option.

---

## Step 5 — Final Recommendation: Server-Sent Events (SSE)

**Reason 1 — Perfect fit for the use case.**
SSE was designed for exactly this: server pushes events to the client.
You're not fighting the protocol to make it work.

**Reason 2 — Zero infrastructure overhead.**
SSE runs over standard HTTP. It works through your existing AWS ALB with no
configuration changes, no sticky sessions, no Redis adapter.

**Reason 3 — Lowest risk for your team and timeline.**
The Node.js implementation is straightforward — under 50 lines to get a
working endpoint. The client uses the native `EventSource` API with no
dependencies. Your team can ship this confidently in 4 weeks.

---

### Getting Started

\```typescript
// Server — Express SSE endpoint
app.get('/notifications/stream', (req, res) => {
  res.setHeader('Content-Type', 'text/event-stream');
  res.setHeader('Cache-Control', 'no-cache');
  res.setHeader('Connection', 'keep-alive');

  // Send a notification event
  const sendEvent = (data: object) => {
    res.write(`data: ${JSON.stringify(data)}\n\n`);
  };

  // Subscribe this connection to user's notifications
  const userId = req.user.id;
  notificationEmitter.on(`notify:${userId}`, sendEvent);

  // Clean up on disconnect
  req.on('close', () => {
    notificationEmitter.off(`notify:${userId}`, sendEvent);
  });
});
\```

\```typescript
// Client — native EventSource API
const stream = new EventSource('/notifications/stream');

stream.onmessage = (event) => {
  const notification = JSON.parse(event.data);
  showNotification(notification);
};

stream.onerror = () => {
  // EventSource reconnects automatically — no manual retry logic needed
  console.warn('SSE connection lost, reconnecting...');
};
\```

**When to revisit this decision:**
Migrate to WebSockets if you need client → server messaging (e.g. live
collaborative editing) or if you scale beyond what a single Node.js
EventEmitter can handle — at that point you'll need a message broker
regardless of the protocol you choose.
```

## Variations

| Scenario | Adapt the prompt by... |
|---|---|
| Database selection | Listing your data access patterns, read/write ratio, and scale in Context |
| Monolith vs microservices | Describing team size, deployment frequency, and domain boundaries |
| Caching strategy | Including your current DB query latency and hottest read paths |
| ORM vs raw SQL | Describing query complexity, team SQL comfort level, and performance needs |
| CI/CD pipeline design | Listing current deployment pain points and team size |
| Frontend framework choice | Describing app type (SPA/MPA), SEO needs, and team experience |

## Tips

**Explicitly say "step by step" — it changes the output.**
Without it, the model skips directly to a recommendation. With it, you see
the reasoning and can catch flawed assumptions before they become bad decisions.

**"What I've already considered" saves time.**
If you've already ruled out an option, say so. Otherwise the model spends
reasoning steps re-explaining something you already know.

**The more constraints you give, the more useful the answer.**
A recommendation that ignores your infrastructure, team experience, or
timeline isn't a recommendation — it's a blog post. Constraints make
the reasoning concrete.

**Ask for the elimination step explicitly.**
Step 4 — "eliminate weak options" — is the most valuable part. It forces
the model to commit to a position rather than giving you a balanced
"it depends" answer with no winner.

**Use this before architecture decisions, not after.**
Chain of thought works best when you're genuinely undecided. If you've
already chosen and want validation, use Role Prompting with a critic role instead.

## Related Prompts

- [role-prompting.md](./role-prompting.md) — assign an expert role to guide the reasoning perspective
- [structured-output.md](./structured-output.md) — format the final recommendation as structured JSON
- [prompt-chaining.md](./prompt-chaining.md) — use CoT output as input to the next step in a pipeline

## Tags

`chain-of-thought` `reasoning` `architecture` `decision-making` `debugging` `development` `prompt-patterns`