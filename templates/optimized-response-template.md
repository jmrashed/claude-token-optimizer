# Optimized Response Template

Use this template for all non-code responses.

---

## Minimal Response Template

```
[Answer or diff code block]
```

**Key principles:**
- Start with the answer
- End after the answer
- No filler text before or after

---

## Diff Response Template

```
[unified diff block]

[1-line note only if necessary]
```

---

## Multi-Step Response Template

```
**Step 1:** [action]
```
[diff/code]

**Step 2:** [action]
```
[diff/code]
```

Max 3 steps per response. Add: "Continued: [request]" to proceed.

---

## Explanation Response Template

```
[Concept] = [30-word max definition]
```

Use bullet table for comparisons. No paragraphs.

---

## Error Response Template

```
Error: [class] at [file:line]
Cause: [max 15 words]
Fix: [command or 1-line change]
```

No stack traces unless explicitly requested.

---

## Confirmation Template

```
Done. [Max 10 word status].
[Diff if applicable]
```

No "Let me know if..." or "I hope this helps!"
