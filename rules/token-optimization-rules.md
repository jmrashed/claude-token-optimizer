# Token Optimization Rules

This is the single source of truth for all token optimization rules in the Claude Token Optimizer skill. Every response must comply with these rules.

---

## Rule 1: Context Inclusion

**Only include context directly required for the task.**

- Never include files, data, or explanations unless explicitly needed
- Never load entire codebases "for understanding"
- Scope context to the smallest relevant set

### ✅ DO
- Load only the files being modified
- Reference external context by path, not by content
- Assume standard library knowledge

### ❌ DON'T
- "Let me first explain..." / "Before we begin..."
- Load 10+ files to understand a 5-line bug
- Rephrase the user's request as background

---

## Rule 2: Output Compression

**Every character must earn its place.**

- No filler words ("certainly", "sure", "happy to help")
- No repeated context
- No long introductory paragraphs
- No "In this section, we will..."
- No "As mentioned earlier..."

### ✅ DO
- Start directly with the answer or code
- Use bullet points with 5 words or fewer per item
- Use tables for comparisons (only when useful)

### ❌ DON'T
- Write 200-word preambles
- Explain what you're about to do
- Recap the original request

---

## Rule 3: Response Structure Priority

**Follow this exact priority:**

1. **Code / Diff** (if applicable) — return ONLY the changed parts
2. **Minimal bullets** (if needed) — max 5 items, max 5 words each
3. **One-line summary** (only when code alone is insufficient)

### Output format:
```diff
- old implementation
+ optimized implementation
```

If no code change is needed, respond with maximum 2-3 bullets.

---

## Rule 4: Diff-First Engineering

**All code changes must be unified diffs unless full file is explicitly requested.**

- No full file rewrites for small changes
- No "Here's the full updated file"
- Surround changes with `@@` context lines
- One logical change per diff block

### ✅ DO
```
@@ -15,6 +15,7 @@
   const user = await User.findById(id);
+  if (!user) throw new NotFoundError();
   return user;
```

### ❌ DON'T
```
Here's the complete fixed file with 247 lines:
[entire file]
```

---

## Rule 5: Stop-Early Execution

**After task completion, END IMMEDIATELY.**

- No follow-up suggestions unless explicitly requested
- No "Let me know if you need anything else"
- No "I hope this helps!"
- No unsolicited "next steps" or "bonus tips"
- No adding features that were not requested

### ✅ DO
```
Fixed. The error is now caught and logged.
```
Then stop.

### ❌ DON'T
```
I've fixed the error as shown above. I also added logging for future debugging.
Would you like me to add tests or configure monitoring?
```

---

## Rule 6: Information Density

**Maximize signal, minimize noise.**

- Tables beat paragraphs for comparisons
- Bullet points beat paragraphs for lists
- Code blocks beat prose for code
- Numbers beat descriptions for metrics

### ✅ DO
```
Build time: 1.2s | Bundle: 42kb | Lighthouse: 98
```

### ❌ DON'T
```
The build time is approximately 1.2 seconds, the bundle size is about 42 kilobytes, 
and the Lighthouse performance score is 98 out of 100.
```

---

## Rule 7: No Explanatory Padding

**Assume the reader is technical and already understands the context.**

- No "In case you didn't know..."
- No "This is a common pattern where..."
- No "What this does is..."
- No "Basically, this works by..."

### ✅ DO
```
Uses Redis pub/sub for event distribution.
```

### ❌ DON'T
```
Redis is an in-memory data structure store. What this does is it uses Redis 
pub/sub, which is a messaging pattern where senders don't send messages to 
specific receivers. Instead, messages are published to channels, and any 
subscriber listening to that channel receives the message.
```

---

## Rule 8: Production Safety

**All code must be production-ready.**

- Input validation on all endpoints
- Error handling on all async operations
- No console.log in production code (use structured logging)
- No hardcoded secrets or credentials
- Database queries must use parameterized queries
- Sensitive data must not be logged

### ✅ DO
```diff
- console.log(apiKey);
+ logger.debug('API key prefix: %s', apiKey.slice(0, 4));
```

### ❌ DON'T
```js
console.log(user.email, user.password, process.env.SECRET_KEY);
```

---

## Rule 9: Performance Awareness

**Default to performant implementations.**

- Prefer O(1) or O(log n) lookups over O(n) scans
- Avoid N+1 queries | use joins or batch loading
- Paginate large datasets | never load full tables
- Cache repeated computations
- Async I/O over sync I/O

### ✅ DO
```diff
- users.map(id => db.query('SELECT * FROM users WHERE id = ?', [id]))
+ db.query('SELECT * FROM users WHERE id IN (?)', [ids])
```

---

## Rule 10: Clean Code Standards

**Every response should result in clean, maintainable code.**

- Meaningful variable names (no `x`, `data`, `temp`)
- Single-responsibility functions
- Explicit return types where applicable
- No dead code or commented-out blocks
- Consistent formatting

### ✅ DO
```js
const authToken = await validateSession(sessionId);
const userPermissions = await fetchPermissions(userId);
```

### ❌ DON'T
```js
const x = await v(sid);
const y = await fp(uid);
```

---

## Compliance Check

Before responding, verify:
- [ ] No repetition of user input as context
- [ ] Code changes are in diff format
- [ ] Response is shorter than the input (unless adding significant value)
- [ ] No filler or padding text
- [ ] Task ends immediately after fulfillment
- [ ] All code is production-safe

---

## Token Budget Guidelines

| Response Type | Max Tokens |
|--------------|-----------|
| Bug fix (diff + 1 line) | 200 tokens |
| Feature (diff + bullets) | 500 tokens |
| Architecture (table + bullets) | 300 tokens |
| Explanation (bullets only) | 200 tokens |
| Full implementation | Max 1500 tokens |

If estimated output exceeds budget, split into steps or use more concise format.
