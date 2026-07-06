# Structured Output — Development

## Description

Instructs the AI to respond in a precise, machine-readable format instead of
natural language prose. Rather than getting a paragraph you need to manually
parse, you get clean JSON, typed interfaces, or structured data you can feed
directly into your application.

Structured output is the bridge between AI responses and production code.
It eliminates brittle string parsing, unpredictable response shapes, and the
gap between what the model returns and what your TypeScript types expect.

## Usage

Define the exact output schema upfront — field names, types, nesting, and
whether fields are required or optional. Instruct the model explicitly to
return only the structure with no extra text, explanation, or markdown fences.

Use this pattern for: code analysis pipelines, AI-powered linting, automated
PR reviews, extracting structured data from unstructured input, generating
typed configuration, building AI features that feed into downstream logic,
and anywhere the response will be parsed programmatically rather than read
by a human.

## Prompt

```markdown
Analyse the following [INPUT TYPE] and return a structured response.

## Input

[Paste your code, text, error log, or data here]

## Output Schema

Return ONLY a valid JSON object matching this exact schema.
No explanation, no markdown fences, no text before or after the JSON.

\```typescript
{
  [field: string]: [type]       // [description of what this field contains]
  [field: string]: [type]       // [description]
  [field: string]: {            // [nested object description]
    [nestedField: string]: [type]
  }[]
}
\```

## Rules

- Return raw JSON only — no \`\`\`json fences, no preamble, no explanation
- If a field has no applicable value, use null — never omit required fields
- Severity must be one of: [ENUM VALUES]
- [Any other constraints on values, enums, or field formats]
```

## Example Input

```markdown
Analyse the following TypeScript function and return a structured code review.

## Input

\```typescript
async function processPayment(userId: string, amount: number, cardToken: string) {
  const user = await db.query(`SELECT * FROM users WHERE id = ${userId}`);

  if (amount <= 0) {
    throw new Error('bad amount');
  }

  const charge = await stripe.charges.create({
    amount: amount,
    currency: 'usd',
    source: cardToken,
    description: 'Payment for user ' + userId,
  });

  await db.query(`UPDATE users SET last_payment = ${Date.now()} WHERE id = ${userId}`);

  console.log('Payment processed: ' + JSON.stringify(charge));

  return charge;
}
\```

## Output Schema

Return ONLY a valid JSON object matching this exact schema.
No explanation, no markdown fences, no text before or after the JSON.

\```typescript
{
  summary: string                  // 1–2 sentence overall assessment
  score: number                    // code quality score from 1–10
  issues: {
    line: number | null            // line number if identifiable, else null
    severity: "critical" | "high" | "medium" | "low"
    category: "security" | "reliability" | "types" | "style" | "performance"
    problem: string                // what is wrong and why it matters
    fix: string                    // specific correction to apply
    codeExample: string | null     // corrected code snippet if applicable, else null
  }[]
  positives: string[]              // what the code does well — empty array if none
  refactoredSignature: string      // improved function signature with proper types
  mustFixBeforeMerge: boolean      // true if any critical or high severity issues exist
}
\```

## Rules

- Return raw JSON only — no \`\`\`json fences, no preamble, no explanation
- If a field has no applicable value, use null — never omit required fields
- severity must be exactly one of: "critical", "high", "medium", "low"
- category must be exactly one of: "security", "reliability", "types", "style", "performance"
- score must reflect issue severity — a critical security issue cannot score above 4
```

## Example Output

```json
{
  "summary": "This function has a critical SQL injection vulnerability and exposes sensitive payment data in logs. It must not be merged in its current state.",
  "score": 2,
  "issues": [
    {
      "line": 2,
      "severity": "critical",
      "category": "security",
      "problem": "String interpolation in a raw SQL query allows SQL injection. An attacker controlling userId can execute arbitrary SQL including data exfiltration or deletion.",
      "fix": "Use parameterised queries for all database interactions.",
      "codeExample": "const user = await db.query('SELECT id, email, role FROM users WHERE id = $1', [userId]);"
    },
    {
      "line": 16,
      "severity": "critical",
      "category": "security",
      "problem": "console.log serialises the full Stripe charge object, which contains the card token, amount, and customer identifiers. This exposes PCI-sensitive data in application logs.",
      "fix": "Log only non-sensitive identifiers — charge ID and status.",
      "codeExample": "logger.info('Payment processed', { chargeId: charge.id, status: charge.status, userId });"
    },
    {
      "line": 14,
      "severity": "critical",
      "category": "security",
      "problem": "String interpolation in the UPDATE query is a second SQL injection vector. An attacker controlling userId can corrupt or delete payment records.",
      "fix": "Use a parameterised query.",
      "codeExample": "await db.query('UPDATE users SET last_payment = $1 WHERE id = $2', [Date.now(), userId]);"
    },
    {
      "line": 4,
      "severity": "high",
      "category": "reliability",
      "problem": "No try/catch around the Stripe API call. A network failure or declined card throws an unhandled exception, giving the caller no way to distinguish a payment failure from a server crash.",
      "fix": "Wrap the Stripe call in a try/catch and throw a typed PaymentError with the Stripe error code attached.",
      "codeExample": null
    },
    {
      "line": 4,
      "severity": "medium",
      "category": "types",
      "problem": "amount is typed as number but Stripe expects amounts in the smallest currency unit (cents for USD). Passing 49.99 instead of 4999 will charge $0.49.",
      "fix": "Type amount as an integer representing cents, or convert and validate before the Stripe call.",
      "codeExample": "const amountInCents = Math.round(amount * 100);"
    },
    {
      "line": 5,
      "severity": "low",
      "category": "style",
      "problem": "'bad amount' is not a useful error message for a payment system. It gives the caller no context and is unloggable.",
      "fix": "Use a descriptive message with the invalid value included.",
      "codeExample": "throw new Error(`Invalid payment amount: ${amount}. Must be a positive number.`);"
    }
  ],
  "positives": [
    "Amount validation is present before hitting the payment provider — prevents unnecessary Stripe API calls for invalid input.",
    "Stripe integration uses the official SDK rather than raw HTTP calls."
  ],
  "refactoredSignature": "async function processPayment(userId: string, amountInCents: number, cardToken: string): Promise<Stripe.Charge>",
  "mustFixBeforeMerge": true
}
```

## Variations

| Scenario | Schema to define |
|---|---|
| Dependency audit | `{ package, currentVersion, latestVersion, breakingChanges, updateRisk }`|
| API endpoint analysis | `{ method, path, authRequired, inputSchema, responseSchema, issues[] }` |
| Error log parsing | `{ timestamp, severity, service, message, rootCause, suggestedFix }` |
| PR description generation | `{ title, summary, changes[], testingNotes, breakingChanges, reviewFocus[] }` |
| Test coverage analysis | `{ coveredPaths[], uncoveredPaths[], riskAreas[], suggestedTests[] }` |
| Environment variable audit | `{ name, required, currentlySet, description, exampleValue }` |
| TypeScript interface generation | `{ interfaceName, fields[], extendsFrom, usageExample }` |
| Database schema review | `{ tableName, issues[], missingIndexes[], normalisationSuggestions[] }` |

## Tips

**"No markdown fences" must be explicit.**
By default the model wraps JSON in \`\`\`json blocks. If you're calling
`JSON.parse()` directly on the response, that breaks. Always say:
`"Return raw JSON only — no fences, no preamble, no explanation."`

**Define enums for every categorical field.**
`severity: string` gives you unpredictable values like "major" or "important".
`severity: "critical" | "high" | "medium" | "low"` gives you values you can
`switch` on without defensive checks.

**Use null, never omit.**
If you allow missing fields, you'll get inconsistent shapes and TypeScript will
complain. Require `null` explicitly for empty values — then your interface can
declare `field: string | null` and always trust the shape.

**Paste the schema as TypeScript, not JSON Schema.**
TypeScript interface syntax is more compact, more readable, and the model
interprets it more accurately than verbose JSON Schema notation. Use
inline comments to explain each field's intent.

**Parse defensively in your code regardless.**
Even with a tight schema, add a try/catch around `JSON.parse()` and validate
the shape with a library like Zod. Treat AI output as untrusted external
data — because it is.

**Score/rating fields anchor the whole response.**
Including a `score: number` or `mustFixBeforeMerge: boolean` forces the model
to make a global judgement before listing issues. This produces more calibrated
severity ratings across the issues array.

## Related Prompts

- [role-prompting.md](./role-prompting.md) — prepend a role to add domain expertise to the analysis
- [few-shot.md](./few-shot.md) — use examples to demonstrate the exact output shape before the schema
- [prompt-chaining.md](./prompt-chaining.md) — feed structured output directly as typed input to the next step

## Tags

`structured-output` `json` `code-analysis` `parsing` `typescript` `production` `development` `prompt-patterns`