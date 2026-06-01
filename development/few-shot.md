# Few-Shot Prompting — Development

## Description

Teaches the AI a pattern by showing it concrete input/output examples before
giving it the real task. Instead of explaining what you want in abstract terms,
you demonstrate it — and the model follows the established pattern precisely.

Few-shot prompting eliminates ambiguity. When words like "clean", "concise", or
"production-ready" mean different things to different people, examples make your
exact standard undeniable. No fine-tuning, no custom models — just examples
inline in the prompt.

## Usage

Provide 2–4 examples that clearly demonstrate the input format, output format,
tone, and level of detail you expect. Then give the real input at the end.
The model completes it by following the pattern you've established.

Use this pattern for: code transformation, commit message generation, error
message standardisation, test case generation, API response formatting,
code comment style, and any task where consistency and format matter more
than creativity.

## Prompt

```markdown
I'm going to show you [NUMBER] examples of [TASK].
Study the pattern carefully — pay attention to [WHAT TO NOTICE:
format / tone / depth / structure / naming conventions].

Then apply the exact same pattern to the new input at the end.

---

## Examples

### Example 1
**Input:**
[First example input]

**Output:**
[First example output]

---

### Example 2
**Input:**
[Second example input]

**Output:**
[Second example output]

---

### Example 3
**Input:**
[Third example input — optional but recommended for complex patterns]

**Output:**
[Third example output]

---

## Now apply the pattern

**Input:**
[Your real input here]

**Output:**
```

## Example Input

```markdown
I'm going to show you 3 examples of converting raw Git diffs into conventional
commit messages. Study the pattern carefully — pay attention to the commit type
prefix, the concise imperative subject line, the bullet-point body describing
what changed and why, and the optional footer for breaking changes.

Then apply the exact same pattern to the new diff at the end.

---

## Examples

### Example 1
**Input:**
\```diff
- export function calculateTotal(items) {
-   let total = 0;
-   items.forEach(item => total += item.price);
-   return total;
- }
+ export function calculateTotal(items: CartItem[]): number {
+   return items.reduce((sum, item) => sum + item.price * item.quantity, 0);
+ }
\```

**Output:**
\```
refactor(cart): rewrite calculateTotal with types and quantity support

- Add CartItem[] type annotation and number return type
- Replace forEach mutation with reduce for immutability
- Factor in item.quantity — previously only summed unit price

No breaking changes — return type and function signature unchanged.
\```

---

### Example 2
**Input:**
\```diff
+ app.use((err: Error, req: Request, res: Response, next: NextFunction) => {
+   console.error(`[${new Date().toISOString()}] ${err.stack}`);
+   res.status(500).json({
+     error: 'Internal server error',
+     requestId: req.headers['x-request-id'],
+   });
+ });
\```

**Output:**
\```
feat(middleware): add global error handler with request ID logging

- Catch unhandled errors at the Express application level
- Log full stack trace with ISO timestamp for traceability
- Return consistent JSON error shape with requestId for client correlation

No breaking changes — additive middleware registration.
\```

---

### Example 3
**Input:**
\```diff
- const user = await User.findOne({ email });
- if (!user) throw new Error('Not found');
- if (user.password !== password) throw new Error('Wrong password');
+ const user = await User.findOne({ email }).select('+password');
+ if (!user || !(await bcrypt.compare(password, user.passwordHash))) {
+   throw new UnauthorizedError('Invalid credentials');
+ }
\```

**Output:**
\```
fix(auth): patch credential comparison and prevent user enumeration

- Use bcrypt.compare instead of plain equality — previous code stored
  plaintext passwords and was vulnerable to timing attacks
- Collapse not-found and wrong-password into a single error message
  to prevent user enumeration via distinct error responses
- Explicitly select passwordHash field — excluded by default in schema

BREAKING CHANGE: clients relying on distinct 'Not found' vs 'Wrong password'
error messages must update error handling logic.
\```

---

## Now apply the pattern

**Input:**
\```diff
- app.get('/users', async (req, res) => {
-   const users = await db.query('SELECT * FROM users');
-   res.send(users);
- });
+ app.get('/users', authenticate, authorize('admin'), async (req, res) => {
+   const users = await db.query(
+     'SELECT id, email, role, created_at FROM users ORDER BY created_at DESC'
+   );
+   res.status(200).json({ data: users.rows, count: users.rowCount });
+ });
\```

**Output:**
```

## Example Output

```markdown
\```
feat(users): secure and standardise GET /users endpoint

- Add authenticate and authorize('admin') middleware — endpoint was
  previously accessible without authentication
- Replace SELECT * with explicit column list to avoid leaking sensitive
  fields such as password hashes and tokens
- Add ORDER BY created_at DESC for consistent, predictable response ordering
- Return structured JSON envelope { data, count } instead of raw array
  for consistent API response shape

BREAKING CHANGE: response shape changed from array to { data: User[], count: number }.
Clients consuming GET /users must update their response handling.
\```
```

## Variations

| Scenario | What to use as examples |
|---|---|
| JSDoc generation | 2–3 functions with their ideal JSDoc comments |
| Error message standardisation | Raw thrown errors → structured AppError objects |
| Test case generation | A function + its ideal describe/it block |
| API response formatting | Raw DB rows → formatted JSON response shapes |
| Code comment style | Uncommented code blocks → your preferred inline comment style |
| PR description writing | A diff → a well-structured PR description |
| TypeScript interface generation | A JSON payload → its TypeScript interface |
| SQL → TypeScript types | A CREATE TABLE statement → a typed interface |

## Tips

**2 examples is the minimum — 3 is the sweet spot.**
One example is too easy to overfit. Two gives a pattern. Three confirms it.
Beyond four, you're wasting context window without meaningful accuracy gains.

**Your examples ARE your specification.**
You don't need to explain the rules in words — the examples demonstrate them.
If your output style is hard to describe, just show it. The model will infer
the pattern more reliably from examples than from instructions.

**Make your examples span the range of variation.**
If your real inputs vary in complexity, include one simple and one complex
example. If edge cases exist (e.g. breaking changes in commit messages),
include an example that demonstrates how to handle them.

**Point out what to notice.**
`"Pay attention to the commit type prefix, imperative subject line, and
bullet-point body"` primes the model before it sees the examples. Without
this, it might focus on the wrong aspect of the pattern.

**Use few-shot when "just describe what you want" keeps failing.**
If you've tried plain instructions and the model keeps getting the format
wrong, switch to examples. Demonstration beats description for format-critical
tasks every time.

**Combine with Structured Output for machine-readable results.**
If you need the output parsed by code, add after the examples:
`"Return the output as valid JSON only — no markdown, no explanation."`

## Related Prompts

- [role-prompting.md](./role-prompting.md) — assign an expert role before the examples for domain-specific tone
- [structured-output.md](./structured-output.md) — enforce a parseable format on top of the few-shot pattern
- [prompt-chaining.md](./prompt-chaining.md) — use few-shot output as structured input for the next pipeline step

## Tags

`few-shot` `code-transformation` `commit-messages` `consistency` `formatting` `development` `prompt-patterns`