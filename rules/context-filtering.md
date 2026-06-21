# Context Filtering Rules

A subset of the Token Optimization Rules. These rules control what context is loaded and used during task execution.

---

## Filter 1: Scope-Based Inclusion

**Only load files directly related to the current task.**

- Modify a user service? Load only `user.service.js` and its tests.
- Debug a login issue? Load `auth.js`, `session.js` — not `payment.js` or `email.js`.
- Need a type? Use the type definition, not the entire module.

### Decision tree:
```
Is this file being modified? → Load it
Is this file directly referenced? → Load it
Is this file in the same directory and likely related? → Consider loading
Otherwise → Do NOT load
```

---

## Filter 2: No Unnecessary Explanations

**Treat explanation requests with the same compression rules as code.**

- When explaining a concept, use the minimum words needed for correctness
- Use metaphors or analogies only when they reduce total token count
- Prefer bullet points or numbered lists over paragraphs

---

## Filter 3: No Pre-Action Rambling

**Never write a paragraph explaining what you're about to do.**

Action | Verbosity
-------|----------
"I'll start by..." | Never
"Let me first..." | Never
"Before we begin..." | Never
"Here's my plan:" | Only if explicitly asked for a plan

Start with the action, not the announcement.

---

## Filter 4: No Question Repetition

**Never restate the user's question or request as part of your response.**

User says: "Fix this bug in auth.js"

Don't write: "You asked me to fix a bug in auth.js. Let me look at the authentication middleware..."

Instead, just fix it.

---

## Filter 5: No "As I Mentioned"

**Never reference your own previous outputs as context.**

Each response stands alone. If context is missing, it means it should have been included in the current input, not referenced from a previous turn.

If something needs prior context, include only the essential snippet.

---

## Filter 6: Project-Wide Knowledge is Free

**You know standard patterns, libraries, and best practices. Don't re-explain them.**

- Don't explain what React is
- Don't explain what JWT authentication is
- Don't explain what REST API means

Only explain project-specific implementation details.

---

## Filter 7: Assumed Capabilities

**Assume the user is technical unless explicitly stated otherwise.**

- No hand-holding through basic concepts
- No defining common terms
- No explaining why you're using a specific approach (show, don't tell)

---

## Filter 8: External Context by Reference

**When broader context is needed, reference it, don't embed it.**

- "Config follows `config/schema.ts` v3 format"
- "Uses the error types from `errors/`"
- "See `docs/API.md` for endpoint contracts"

NEVER paste the full schema, config, or documentation into the response.

---

## Filter 9: Stack Trace filtering

**When debugging, present only the actionable frames.**

- Show the line that errored
- Show 1-2 lines of surrounding context
- Omit the full stack trace unless requested

```
Error: Connection refused at db.connect (db.js:23:5)
```

NOT:
```
Error: Connection refused
    at TCP.onWrapError [as onerror] (node:internal/stream_base_commons:99:12)
    at TLSSocket.<anonymous> (node:_http_client:153:33)
    ... [30 more lines]
```

---

## Filter 10: Output Context Minimization

**The output context should be smaller than the input context (ideally).**

If the input is 500 tokens and your output is 2000 tokens, you're doing it wrong for a code task.

For explanation tasks, output should add value, not volume.

---

## Filter 11: Time-Sensitive Context

**Only include "when to use" advice if the user's timeline is provided.**

- No: "This approach is best for small teams starting in 2024"
- No: "In production, you should always..."
- Yes: "Use this when your team already uses Redis"

---

## Filter 12: No Multi-Turn Unrolling

**Don't anticipate future requests or add "just in case" content.**

- No: "I've also added X in case you need it later"
- No: "Here's Y too, for future reference"
- Include only what's needed RIGHT NOW
