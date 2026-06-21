# Before vs After Optimization Examples

This document demonstrates how Claude Token Optimizer transforms verbose, token-heavy outputs into concise, production-ready responses.

---

## Example 1: Simple Code Fix

### ❌ BEFORE (Verbose)

```
Hi! I'd be happy to help you fix that bug in your authentication function.

Looking at your code, I can see that the issue is related to how the JWT token is being validated. The problem occurs because the expiration time isn't being checked properly.

Here's what's happening:
1. The token is being generated correctly in the login endpoint
2. But when validating in the middleware, the expiration check is missing
3. This allows expired tokens to be processed

To fix this, I would suggest the following approach:
- First, let's add the expiration check
- Then we need to update the error handling
- Finally we should add some logging

Here's the complete fixed file with all changes:

[Entire 200-line file rewritten with minor changes embedded]

Additional notes:
- Make sure to install the jsonwebtoken package
- You might want to add tests for this
- Consider adding rate limiting as well
```

---

### ✅ AFTER (Optimized)

```diff
--- middleware/auth.js
+++ middleware/auth.js
@@ -12,7 +12,8 @@
   jwt.verify(token, process.env.JWT_SECRET, (err, decoded) => {
     if (err) {
-      return res.status(401).json({ message: 'Invalid token' });
+      const isExpired = err.message === 'jwt expired';
+      return res.status(isExpired ? 401 : 403).json({
+        message: isExpired ? 'Token expired' : 'Invalid token'
+      });
     }
     req.user = decoded;
     next();
   });
```

**Token savings: ~78% | Time saved: significant**

---

## Example 2: Architecture Design

### ❌ BEFORE (Wasteful)

```
I'll help you design a microservices architecture for your e-commerce platform.

Before we begin, let me explain what microservices are. Microservices architecture is an approach where... [300 words of explanation]

For your specific use case, here's what I recommend:

First, let's think about the domain boundaries:
- Users service: handles authentication, profiles, preferences
- Products service: handles catalog, inventory, pricing
- Orders service: handles cart, checkout, fulfillment
- Payments service: handles transactions, refunds

Now let me explain each service in detail...

[500+ words of explanation]

Here's a complete project structure:
[50+ file tree]

And here are all the Docker compose files, API routes, database schemas, and deployment configurations...
```

---

### ✅ AFTER (Optimized)

**Services overview:**

| Service | Responsibility | Tech Stack |
|---------|---------------|------------|
| users | Auth, profiles | Node.js, PostgreSQL |
| products | Catalog, inventory | Node.js, PostgreSQL |
| orders | Cart, checkout | Node.js, PostgreSQL |
| payments | Transactions | Node.js, Stripe API |

**API Gateway routes:**
```
/api/users/*    → users:3000
/api/products/* → products:3001
/api/orders/*   → orders:3002
/api/payments/* → payments:3003
```

**Key decisions:**
- Event-driven via message queue
- Shared PostgreSQL or per-service cluster in V2
- Include `docker-compose.yml`, service configs, and CI on request

**Token savings: ~85% | Context preserved: critical only**

---

## Example 3: Bug Debugging

### ❌ BEFORE (Blast radius)

```
I see you're having an issue with the payment processing. Let me help you debug this!

First, let me explain how payment processing typically works in Node.js applications. There are several components involved... [200 words]

Then let me look at your code structure. I can see you're using Stripe, which is great. Let me load all your related files:
- payment.controller.js
- payment.service.js
- payment.model.js
- stripe.config.js
- webhook.js
- 10 other unrelated files

Now let me analyze all of them...

[Massive output with full file rewrites and changes I didn't need to make]
```

---

### ✅ AFTER (Optimized)

**Root cause:** `payment.service.js:45` — missing `try-catch` on `stripe.charges.create()`, causing unhandled rejection to crash the worker.

**Fix:**

```diff
--- services/payment.service.js
+++ services/payment.service.js
@@ -42,6 +42,11 @@
-  const charge = await stripe.charges.create({...});
+  try {
+    const charge = await stripe.charges.create({...});
+    webhookLogger('charge_succeeded', charge.id);
+    return charge;
+  } catch (err) {
+    webhookLogger('charge_failed', { error: err.message });
+    throw new PaymentError('Processing failed', err);
+  }
```

Unrelated files not loaded. Fix only.

---

## Example 4: Technology Recommendation

### ❌ BEFORE (Overwhelmed)

```
That's a great question! Let me help you choose between React and Vue for your project.

First, let me explain what React is. React is a JavaScript library developed by Facebook in 2013... [200 words]

Then let me explain what Vue is. Vue is a progressive JavaScript framework created by Evan You in 2014... [200 words]

Now, let me give you a detailed comparison:

[Table with 20+ dimensions]

Then let me look at your project requirements:
- Are you building a SPA or SSR?
- What about state management?
- What about routing?
- What about testing?
- What about deployment?
- ...

I recommend React because...
[500 more words]
```

---

### ✅ AFTER (Optimized)

**React if:** existing codebase uses React, need ecosystem (Next.js, React Native), large team.

**Vue if:** greenfield project, smaller team, need faster ramp-up, lighter bundle.

**Recommendation: React** — team already uses it; aligns with existing stack.

**Token savings: ~70% | Actionable answer: first sentence**

---

## Key Metrics

| Scenario | Before (tokens) | After (tokens) | Savings |
|----------|----------------|---------------|---------|
| Bug fix | ~2,400 | ~520 | 78% |
| Architecture | ~3,800 | ~580 | 85% |
| Debugging | ~4,100 | ~890 | 78% |
| Tech recommendation | ~3,200 | ~950 | 70% |

---

## The Pattern

Every optimized response follows the same rules:

1. **Never repeat the question or requirements**
2. **Never rewrite code I didn't write**
3. **Return diffs, not full files**
4. **Stop after the task is done**
5. **Use tables/bullets over paragraphs**
6. **Remove all filler text**
7. **Load only what I need**
