# After: Optimized Claude Response (Minimal Token Usage)

This example shows the same task after applying Claude Token Optimizer rules.

---

## Scenario: "Fix the bug in auth.js where expired JWT tokens are not being rejected"

### Optimized Claude Response (After Optimization)

```diff
--- middleware/auth.js
+++ middleware/auth.js
@@ -8,7 +8,12 @@
   jwt.verify(token, process.env.JWT_SECRET, (err, user) => {
     if (err) {
-      return res.status(403).json({ message: 'Invalid token' });
+      if (err.name === 'TokenExpiredError') {
+        return res.status(401).json({
+          message: 'Token expired',
+          code: 'TOKEN_EXPIRED'
+        });
+      }
+      return res.status(403).json({ message: 'Invalid token' });
     }
     req.user = user;
     next();
```

---

## Token Analysis

| Metric | Value |
|--------|-------|
| Total tokens | ~280 |
| Useful tokens | ~270 |
| Waste ratio | 3.6% |
| Time to find answer | ~1 second |
| Time to apply fix | ~5 seconds |

### Breakdown:
- Diff block only: ~270 tokens
- No greeting, no explanation, no filler

**Savings: 1,570 tokens (85% reduction)**

---

## What Changed

| Aspect | Before | After |
|--------|--------|-------|
| Greeting | ✅ Present | ❌ Removed |
| Problem restatement | ✅ Full recap | ❌ None |
| What will be done | ✅ 5-step plan | ❌ Action only |
| The actual fix | Embedded in full file | Diff only |
| Why it works | ✅ Explained | ❌ Not restated |
| Recommendations | ✅ 4 suggestions | ❌ None |
| Closing | ✅ Offer to help more | ❌ None |
| Format | Full file rewrite | Unified diff |

---

## This is how Claude Token Optimizer works.

Apply the same rules:
1. **Diff-first** — return only changed code
2. **No repetition** — don't restate the problem
3. **No filler** — no greetings, no closings
4. **No redundancy** — don't explain what the code shows
5. **Stop early** — end after the fix

> Rule: Output must be shorter than the input for the same task.
