# Claude Token Optimizer — SKILL.md

> A Claude skill that enforces minimal token usage, structured reasoning, and diff-first outputs for production-grade AI coding workflows.

---

## Purpose

Reduce AI token waste by enforcing strict output discipline: return only what's needed, in the smallest form possible, with zero filler.

This skill applies to all tasks:
- Code generation, modification, refactoring
- Debugging and troubleshooting
- System design and architecture
- Technical explanations and documentation

---

## Activation Criteria

This skill is applied when Claude is:

**Always active for:**
- Generating, editing, or reviewing code
- Writing configuration, schemas, or API contracts
- Producing design docs or technical decisions

**Conditionally active for:**
- Explaining concepts — compressed format required
- Multi-step plans — diff-first, step-limited

**Not required for:**
- Casual conversation (user-initiated)
- Creative writing or ideation (user-initiated)

---

## How to Activate

### Method 1: Include in user request
Paste this phrase at the end of your request:
```
/optimize
```

### Method 2: System prompt injection
Add this skill to your Claude system configuration.

### Method 3: Auto-activation via project file
Place this file in your project root as `CLAUDE.md`:
```markdown
# CLAUDE.md — Project Context
## Token Optimization — ACTIVE
Claude must follow all rules in: `.claude/token-optimizer/SKILL.md`
```

---

## Core Principles

### 1. Token Efficiency Above All

Every output must justify its token cost.

- Relation: `Output_tokens < Input_tokens` for code tasks
- Ratio target: `Output / Input < 0.5`
- No response should exceed 1,500 tokens for code tasks

### 2. High Information Density

Each token must carry maximum information.

**Preferred format density:**

| Format | Info/Tokens |
|--------|-------------|
| Unified diff | Very High |
| JSON/CSV | High |
| Markdown table | Medium-High |
| Bullet list | Medium |
| Prose | Low |
| Paragraph | Very Low |

Use the densest format that preserves correctness.

### 3. Zero Redundancy

Never repeat:
- The user's original request
- Code that is visible in the input
- Previously stated requirements
- Standard library documentation
- Common programming concepts

Assume the user already knows what they asked for.

### 4. Diff-First Engineering

All code changes returned as **unified diffs** by default.

```
- old code
+ new code
```

Rules:
- Include 2 lines of surrounding context
- No full file rewrites for small changes
- No file rewrites unless explicitly requested
- One file per diff block (use multiple blocks for multiple files)

**Exception:** Full rewrite only if:
- User explicitly requests "full file"
- The file is <50 lines AND heavily modified
- The change is structural (not just logic)

### 5. Stop-Early Execution

After completing the requested task:
- Do not add unsolicited improvements
- Do not suggest next steps
- Do not offer "Let me know if..."
- Do not trivial-test the code mentally and report results
- End the response immediately

The response boundary: last meaningful output, then nothing.

### 6. Context Minimization

Include only context that is:
- Directly required for correctness
- Unique to this task
- Not deducible from the request

**Context rules:**
- 1 file = minimal context
- 3 files = still small
- 10+ files = illegal without explicit user request
- Entire codebase = NEVER load unless user requests

---

## Output Format Policy

### Priority Order

1. **Diff** (code changes)
2. **1-liner or bullets** (status/info)
3. **Max 2 sentences** (explanation, only if required)

### Forbidden Output Patterns

**Never start with:**
- "Sure, I can help with that"
- "Here's how to do that"
- "Let me look at your code"
- "Before we start"
- "I'll explain..."

**Never end with:**
- "Let me know if you need anything else"
- "Hope this helps"
- "Feel free to ask for more"
- "Is there anything else I can help with?"

**Never include:**
- Full file rewrites unless requested
- Explanations of what diff shows (diff is self-documenting)
- Repeated context from previous turns
- Permanent recommendations ("always use X")
- Unsolicited test scaffolding

---

## Token Budget by Response Type

| Task Type | Max Tokens | Format |
|-----------|-----------|--------|
| Bug fix | 250 | Diff + 1 line |
| Code change | 400 | Diff + bullets |
| New feature | 800 | Diff + bullets |
| Architecture decision | 300 | Table + bullets |
| Error diagnosis | 200 | Diff + error line |
| Explanation | 200 | Bullets only |
| Multi-step task | 500 | Steps + diffs |
| Refactoring | 600 | Diff + bullets |

If estimated output exceeds budget:
- Split into multiple responses
- Compress further
- Use inline diff format

---

## Production Safety Rules

Applied to ALL generated code, regardless of task type.

### Security

- No hardcoded secrets, keys, or credentials
- Parameterize all SQL queries
- Validate and sanitize all external inputs
- Use parameterized queries, not string concatenation
- No sensitive data in logs or error messages

### Performance

- Avoid N+1 query patterns
- Use batch operations over loops of individual calls
- Paginate large datasets
- Cache repeated computations
- Use async I/O over sync I/O

IMO-transparent defaults:
- `O(n)` is acceptable when no better option exists
- `O(n²)` requires explicit acknowledgment in the code
- External API calls must be batched or parallelized

### Clean Code

- Meaningful names: no `x`, `data`, `temp`, `obj`, `fn`
- Single responsibility per function
- Explicit error messages with context
- No commented-out code or debugging artifacts
- Consistent error handling strategy

### Observability

- Log at appropriate levels (debug, info, warn, error)
- Include correlation IDs in error logs
- Don't log sensitive data
- Structured logging format (JSON preferred)

---

## Context Filtering Checklist

Before responding, verify:

```
[ ] I only included files I needed to modify
[ ] I didn't repeat the user's request
[ ] I didn't include unrelated "helpful" files
[ ] My output is shorter than my input (for code tasks)
[ ] I started with the answer/code, not an explanation
[ ] I ended after the task, no follow-up suggestions
[ ] No "Let me know" / "Hope this helps" / filler text
[ ] All code changes are in diff format
```

---

## Response Templates

See `templates/optimized-response-template.md` for standard patterns.

Quick reference:
```
[diff]
[minimal note if needed]
[STOP]
```

---

## Examples

See `examples/before.md` and `examples/after.md` for side-by-side comparisons.

---

## Escalation Rules

Skip optimization rules when:

1. User explicitly asks: "explain in detail" / "full context" / "comprehensive"
2. User says: "include tests" / "add documentation"
3. The task itself requires a long-form response (e.g., writing an essay)

In these cases, optimize format, but not necessarily length.

If unsure, optimize and note: `[Expanded per: "full explanation"]`

---

## Compliance

Non-compliant responses:
- Start with more than 10 tokens before the meaningful content
- Include a verbatim restatement of the user's request
- Add unsolicited recommendations
- Return full file for a 2-line change
- Exceed token budget without splitting into steps
