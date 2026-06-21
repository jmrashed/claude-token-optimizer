# Output Compression Rules

A subset of the Token Optimization Rules. These rules control how responses are formatted, structured, and compressed.

---

## Compression 1: Word-Level Efficiency

**Every word must justify its existence.**

Remove the first word: "Sure, here's..." → "Here's..." → ""

Remove the first sentence: "I can help with that. The fix is..." → "Fix:"

Remove the conclusion: "Let me know if you need anything else." → (deleted)

Remove hedging: "You might want to consider..." → "Consider..."

Remove softeners: "I think this should..." → "This should..."

### Before → After
| Verbose | Optimized |
|---------|-----------|
| "Sure, I can help with that!" | (removed) |
| "Here's what you need to do:" | "Do:" |
| "I would suggest that you..." | "Use..." |
| "This is a common pattern..." | (removed) |
| "I hope this helps!" | (removed) |

---

## Compression 2: Sentence Merging

**Combine related observations into single lines.**

```
# Before (5 lines)
The user model has three fields: name, email, and role.
The name field is required and must be a string.
The email field is also required and must be unique.
The role field defaults to 'user'.

# After (1 line)
user: { name: string (req), email: string (req, unique), role?: 'admin' | 'user' }
```

---

## Compression 3: Paragraph → Structured Format

**Never output a paragraph when a list or table works.**

| Paragraph | Structured |
|-----------|------------|
| The API has three endpoints: users, products, and orders. Each endpoint supports GET and POST. The users endpoint handles authentication. | `GET/POST: /users, /products, /orders` |

---

## Compression 4: Keyword Substitution

**Use keywords instead of phrases.**

| Phrase | Replace with |
|--------|-------------|
| "it is important to note that" | → "" (remove) |
| "in order to" | "to" |
| "due to the fact that" | "because" |
| "at this point in time" | "now" |
| "in the event that" | "if" |
| "prior to" | "before" |
| "in the process of" | "while" |
| "a large number of" | "many" |

---

## Compression 5: Remove Redundant Qualifiers

**Strip words that don't add meaning.**

| With qualifier | Without |
|----------------|---------|
| "The entire user authentication system" | "The auth system" |
| "A very important consideration" | "Key consideration:" |
| "In this particular case" | "Here" / (remove) |
| "The complete set of changes" | "Changes:" |
| "All of the files" | "Files:" |

---

## Compression 6: Code Comment Minimization

**Comments should add value, not restate the code.**

```diff
 function calculateTotal(items) {
-  // Iterate through all items
   let total = 0;
-  // Loop over each item
   for (const item of items) {
-  // Add item price to total
     total += item.price;
   }
-  // Return the final total
   return total;
 }
```

**Good comments** (add context not in code):
```js
// Capped at 1000 to prevent decimal overflow in legacy DB
return Math.min(cartTotal, 1000);
```

---

## Compression 7: Number Formatting

**Use compact number representations.**

| Verbose | Optimized |
|---------|-----------|
| "approximately 1,200" | "~1.2k" |
| "more than 500" | ">500" |
| "less than 50" | "<50" |
| "between 10 and 100" | "10–100" |
| "about 2.5 milliseconds" | "~2.5ms" |

---

## Compression 8: Remove "We" and "Let's"

**Remove collaborative language from technical responses.**

| Verbose | Optimized |
|---------|-----------|
| "We can fix this by..." | "Fix:" |
| "Let's update the config..." | "Update config:" |
| "We'll need to add..." | "Add:" |

---

## Compression 9: List Optimization

**Drop articles, use symbols, compress labels.**

```
# Before
The following items need to be checked:
1. The database connection string
2. The API key configuration
3. The environment variable settings

# After
Check: `DB_URI`, `API_KEY`, env vars
```

---

## Compression 10: List Slicing

**Maximum output length is 5 lines for bullets/lists.**

| Verbose List | Optimized |
|--------------|-----------|
| 10+ bullet items | 3-5 max, rest "etc." or add in follow-up |
| Long procedure steps | "Steps: 1) ... 2) ... 3) ..." (1 line) |

### Rules for lists
- Max 5 bullets per response
- Max 5 words per bullet
- Combine related items with `&` or `,`
- Use `()` for sub-options: `Auth (JWT/OAuth)`

---

## Compression 11: Remove Self-Referencing

**Never reference your own output as input.**

| ❌ Don't | ✅ Do |
|----------|-------|
| "As mentioned above" | (remove, stand alone) |
| "In my previous response" | (remove, include essential info) |
| "The fix I showed earlier" | (re-state the fix if needed) |

Each response must be self-contained.

---

## Compression 12: Condense File References

| Verbose | Optimized |
|---------|-----------|
| "See the file at `src/utils/helpers.js`" | "`src/utils/helpers.js`" |
| "The configuration can be found in `.env`" | "`.env`" |
| "Refer to `docs/API.md` for more details" | "`docs/API.md`" |

---

## Compression 13: Remove Redundant Examples

**One example is enough.**

| ❌ Don't | ✅ Do |
|----------|-------|
| Show 3 examples of the same pattern | Show 1 with "etc." |
| Repeat the same diff in prose + code | Code only |

---

## Compression 14: Status Report Compression

**Minimal progress reporting.**

| ❌ Don't | ✅ Do |
|----------|-------|
| "I've completed the first step..." | (show the result) |
| "Now moving on to the next part..." | (show next part) |
| "Finally, I've..." | (show final result) |

Progress is indicated by output presence/absence, not narration.

---

## Compression 15: Error Message Compression

**Crisp, actionable error reports.**

```
# Before
I'm sorry but I encountered an error while trying to process your request.
The error message is: "Connection refused at localhost:5432". 
This typically means that your PostgreSQL database is not running. 
You might want to check...

# After
Error: DB connection refused (localhost:5432).
Fix: `docker start postgres`
```

---

## Compression Checklist

Before sending, verify:
- [ ] First word is not filler ("Sure, ", "Okay, ", "Here's...")
- [ ] No paragraph can be reduced to a single line
- [ ] All bullets are 5 words or less
- [ ] No repeated facts
- [ ] Length is appropriate for token budget
- [ ] Every sentence earns its place
