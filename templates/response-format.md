# Optimized Response Format

Standard response formats for all Claude Token Optimizer outputs. Use these formats to maintain consistency and minimize token usage.

---

## Format 1: Diff Response

Used for all code modifications. This is the **primary and preferred format**.

```diff
--- path/to/file.ext
+++ path/to/file.ext
@@ -line,count  optional heading
 context line
-removed line
+added line
 context line
```

**Rules:**
- Only include changed lines with 2 lines of context
- Add file header for the first diff block only
- Multiple files = separate diff blocks
- No explanatory text between code and diff (unless essential)

---

## Format 2: Bullet Response

Used for answers, status reports, and non-code responses.

**Simple status:**
```
Done. Auth middleware now validates token expiry.
```

**Multiple items:**
```
Changes:
• auth.js — added expiry check (line 15)
• auth.test.js — added expiry test case
```

**Status report:**
```
Status:
✓ JWT validation fixed
✓ Tests passing
Remaining: deploy `staging` branch
```

**Rules:**
- Max 5 bullets per response
- Max 5 words per bullet
- Use symbols: ✓, ✗, →, :: , =, +, -
- No full sentences

---

## Format 3: Table Response

Used for comparisons, trade-offs, and structured data.

```markdown
| Criteria | Option A | Option B |
|----------|----------|----------|
| Setup    | 30 min   | 15 min   |
| Scale    | High     | Medium   |
| Cost     | $$       | $        |
```

**Rules:**
- Only when comparison adds clear value
- Max 5 rows, max 4 columns
- Left-align, no text wrapping

---

## Format 4: Status Line

Used for quick confirmations and acknowledgments.

```
Done. | Status: [status] | Changed: [file.ext]
```

Examples:
```
Done. | Status: deployed to staging | Changed: api/routes.ts
Done. | Status: auth now enforced | Changed: middleware/auth.ts
Done. | Status: tests passing | Changed: none
```

---

## Format 5: Error Report

```markdown
Error: `[ClassName]` at `[file:line]`

Cause: [max 15 words]

Fix: [command or 1-line code change]
```

**Rules:**
- No stack trace (unless explicitly requested)
- Max 15 words for cause
- Fix must be immediately actionable

---

## Format 6: Step-by-Step

For multi-step tasks requiring sequential execution.

```markdown
**Step 1:** [1-line action]
```
[diff/code]
```

**Step 2:** [1-line action]
```
[diff/code]
```

**Remaining:** [next step]
```

**Rules:**
- Max 3 steps per response
- "Remaining:" indicates continuation
- No prose between steps

---

## Format 7: Minimal Text Response

When code alone is insufficient.

```
[Max 2 sentences, max 100 words total]
Use: `$command --flag`
```

**Rules:**
- Max 100 words
- Max 2 sentences
- No filler ("Sure, ", "Here's")
- Include CLI examples inline

---

## Format Priority

When choosing a format:

1. **Diff** — code changes being made
2. **Bullets** — status, lists, simple info
3. **Table** — structured comparison
4. **1-liner** — confirmation, status
5. **Error** — bug reports
6. **Steps** — multi-step tasks
7. **Text** — minimal prose (last resort)

---

## Universal Constraints

All formats must follow:
- No introductory sentences before the format
- No concluding sentences after the format
- No repeating the question/request
- No suggestions for next steps (unless requested)
- No filler text: no "Sure,", no "Hope this helps!", no "Let me know!"
- No explanations of what you're about to do (just do it)

---

## Format Violations (Examples)

### ❌ Wrong
```
Here's the fix for your authentication issue:
```diff
...
```
Let me know if you need anything else!
```

### ✅ Right
```diff
...
```
