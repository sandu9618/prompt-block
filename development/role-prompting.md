# Role Prompting — Development

## Description

Assigns a professional engineering persona to the AI before issuing a task.
By defining a specific role with seniority, domain, and context, you activate
a consistent tone, vocabulary, and reasoning style — making responses more
targeted, opinionated, and actionable for software development scenarios.

## Usage

Define the role with seniority, domain, and optional company/industry context.
Then give the task with clear expectations on format and depth. Works best when
you match the role to the output quality you need — the more specific the persona,
the more precise the response.

Use this pattern for: code review, architecture decisions, debugging sessions,
documentation writing, security audits, and any task where domain expertise
shapes the answer.

## Prompt

```markdown
You are a [SENIORITY] [ROLE] with [X] years of experience in [DOMAIN/SPECIALIZATION].
You work at [TYPE OF COMPANY — e.g. high-traffic SaaS startup, enterprise fintech, open-source project].

Your approach is [STYLE — e.g. direct and opinionated / methodical and thorough / pragmatic over perfect].
Assume I have [LEVEL — beginner / intermediate / senior] knowledge of [TECHNOLOGY].

---

## Task

[Describe what you want clearly. One task per prompt.]

## Context

[Paste relevant code, error messages, system description, or background information here.]

## What I need from you

1. **[Output section 1]** — [what it should contain]
2. **[Output section 2]** — [what it should contain]
3. **[Output section 3]** — [what it should contain]

## Constraints

- [Any constraints on the response — e.g. "don't suggest a full rewrite", "stay within Node.js ecosystem"]
- [Technology constraints — e.g. "we are on PostgreSQL, not MongoDB"]
- [Style constraints — e.g. "be direct, don't soften criticism"]
```

## Example Input

```markdown
You are a senior TypeScript engineer with 10 years of experience building
production-grade REST APIs at scale. You work at a high-traffic fintech startup
where security and reliability are non-negotiable.

Your approach is direct and opinionated — you point out problems clearly,
explain why each is a problem, and always suggest a specific fix.
Assume I have intermediate TypeScript knowledge.

---

## Task

Review the following user service function for correctness, security,
and TypeScript best practices.

## Context

\```typescript
async function getUser(id: string) {
  const user = await db.query(`SELECT * FROM users WHERE id = ${id}`);
  return user;
}
\```

This function is called by our authentication middleware on every request.
We're running Node.js 20, PostgreSQL 15, and using the `pg` library directly.

## What I need from you

1. **Security issues** — flag any vulnerabilities with severity (Critical / High / Medium / Low)
2. **TypeScript issues** — missing types, unsafe patterns, anything the compiler should catch
3. **Reliability issues** — error handling, edge cases, failure modes
4. **Fixed version** — a corrected implementation with inline comments explaining each change

## Constraints

- Stay within the `pg` library — don't suggest switching to an ORM
- Don't suggest a full architectural rewrite — fix this function only
- Be direct, don't soften criticism
```

## Example Output

```markdown
## Security Issues

### 🔴 Critical — SQL Injection
**Problem:** String interpolation in a raw SQL query. An attacker passing
`id = "1; DROP TABLE users"` can execute arbitrary SQL against your database.
This is especially dangerous since this function runs on every auth request.

**Fix:** Use parameterized queries.
\```typescript
const result = await db.query('SELECT * FROM users WHERE id = $1', [id]);
\```

---

## TypeScript Issues

### 🟡 Medium — Missing Return Type
**Problem:** No explicit return type. TypeScript infers `Promise<any>`, which
means callers get no type safety on the returned user object. Bugs from
mismatched field names won't be caught at compile time.

**Fix:** Define and use a return type.
\```typescript
async function getUser(id: string): Promise<User | null>
\```

### 🟡 Medium — No Input Validation
**Problem:** `id` is typed as `string` but there's no runtime check. An empty
string or non-UUID value will hit the database unnecessarily.

**Fix:** Validate before querying.
\```typescript
if (!id || !isValidUUID(id)) return null;
\```

---

## Reliability Issues

### 🟠 High — No Error Handling
**Problem:** If the database query fails (connection timeout, constraint error),
the exception propagates uncaught. Since this runs in auth middleware, an
unhandled DB error will crash the request with a 500 and no useful message.

**Fix:** Wrap in try/catch, distinguish between not-found and DB failure.

### 🟡 Medium — SELECT *
**Problem:** Fetches all columns, including sensitive fields like password hashes
and tokens. Any accidental logging or serialization of this object leaks
credentials.

**Fix:** Explicitly list the columns you need.
\```typescript
SELECT id, email, role, created_at FROM users WHERE id = $1
\```

---

## Fixed Implementation

\```typescript
import { isValidUUID } from '../utils/validation';
import { db } from '../lib/db';

export interface User {
  id: string;
  email: string;
  role: 'admin' | 'user';
  createdAt: Date;
}

/**
 * Fetches a user by ID.
 * Returns null if not found or if the ID is invalid.
 * Throws on unexpected database errors.
 */
export async function getUser(id: string): Promise<User | null> {
  // Validate input before hitting the database
  if (!id || !isValidUUID(id)) return null;

  try {
    // Parameterized query prevents SQL injection
    // Explicit column list avoids leaking sensitive fields
    const result = await db.query<User>(
      'SELECT id, email, role, created_at AS "createdAt" FROM users WHERE id = $1',
      [id]
    );

    // pg returns rows array — return first row or null
    return result.rows[0] ?? null;
  } catch (error) {
    // Re-throw with context so upstream error handlers can log properly
    throw new Error(`getUser failed for id=${id}: ${(error as Error).message}`);
  }
}
\```
```

## Variations

| Scenario | Role to Use |
|---|---|
| Code review | `senior TypeScript engineer with experience in production APIs` |
| Architecture planning | `solutions architect specializing in scalable Node.js systems` |
| Security audit | `penetration tester specializing in Node.js and REST APIs` |
| Performance optimization | `performance engineer at a high-traffic SaaS company` |
| Database design | `database architect with deep PostgreSQL expertise` |
| API design | `API designer who follows REST and OpenAPI 3.0 standards` |
| Frontend review | `senior React engineer focused on accessibility and Core Web Vitals` |
| DevOps guidance | `DevOps engineer with expertise in Kubernetes and CI/CD pipelines` |
| Debugging | `staff engineer doing root cause analysis on a production incident` |
| Documentation | `technical writer with a software engineering background` |

## Tips

**Seniority changes the output.**
`"You are an engineer"` returns generic answers.
`"You are a staff engineer at a high-traffic fintech startup"` returns
opinionated, context-aware answers. Always specify seniority.

**The "What I need from you" section is the most important part.**
Without it, the model decides what to include. With it, you get a structured
response you can act on immediately. Number your expected outputs.

**Add behavioural constraints.**
Shape how the AI responds, not just what role it plays:
- `"Be direct — don't soften criticism"`
- `"Assume I have intermediate TypeScript knowledge"`
- `"Prioritise security over performance in your recommendations"`

**Domain context changes recommendations.**
`"senior engineer"` vs `"senior engineer at a healthcare startup handling PHI data"`
produces very different security recommendations. Add industry context when it
changes the answer.

**Combine with Chain of Thought for complex tasks.**
For architecture or debugging prompts, append:
`"Think through this step by step before giving your recommendation."`

**The role sets the ceiling.**
If you assign a junior role, you get junior-level output. Always assign
the seniority level whose output you actually want.

## Related Prompts

- [chain-of-thought.md](./chain-of-thought.md) — pair with role prompting for multi-step reasoning
- [structured-output.md](./structured-output.md) — add output formatting constraints to role prompts
- [prompt-chaining.md](./prompt-chaining.md) — use role prompting as the first step in a pipeline

## Tags

`role-prompting` `code-review` `architecture` `debugging` `development` `prompt-patterns`