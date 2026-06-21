# Before: Typical Claude Response (Heavy Token Usage)

This example shows how Claude typically responds without optimization.

---

## Scenario: "Fix the bug in auth.js where expired JWT tokens are not being rejected"

### Typical Claude Response (Before Optimization)

```
Hi! I'd be happy to help you fix the JWT token bug in your authentication middleware. Let me take a look at your code to understand the issue first.

Looking at your `auth.js` file, I can see the problem. The `jwt.verify()` method is being called, but the error handling doesn't specifically check for token expiration. When a JWT token expires, the `jwt.verify()` method throws a `JsonWebTokenError` with the message "jwt expired", but your current code treats all errors the same way by returning a 401 status with a generic "Invalid token" message.

Here's what's happening:
1. When a token expires, `jwt.verify()` throws an error
2. Your catch block catches all errors uniformly
3. The user gets a generic "Invalid token" response
4. The client doesn't know if the token was expired or truly invalid

To fix this issue, I would suggest the following approach:

First, we need to identify if the error is specifically a token expiration error. The `jwt.verify()` method throws an error with `err.name === 'TokenExpiredError'` when the token has expired. We can check for this specific condition and return a more appropriate response.

Here's the complete fixed file:

```
const jwt = require('jsonwebtoken');

function authenticateToken(req, res, next) {
  const authHeader = req.headers['authorization'];
  const token = authHeader && authHeader.split(' ')[1];

  if (!token) {
    return res.status(401).json({ message: 'Access token required' });
  }

  jwt.verify(token, process.env.JWT_SECRET, (err, user) => {
    if (err) {
      // Check if the error is specifically a token expiration error
      if (err.name === 'TokenExpiredError') {
        return res.status(401).json({ 
          message: 'Token expired',
          code: 'TOKEN_EXPIRED',
          expiredAt: err.expiredAt
        });
      }
      // Handle other JWT errors
      return res.status(403).json({ 
        message: 'Invalid token',
        code: 'INVALID_TOKEN'
      });
    }
    req.user = user;
    next();
  });
}

module.exports = { authenticateToken };
```

I've made the following changes:
1. Added a specific check for `TokenExpiredError` using `err.name`
2. Returned a distinct response with a `code` field for expired tokens
3. Kept the generic "Invalid token" response for other JWT errors
4. Added `expiredAt` timestamp for debugging purposes

This approach is better because:
- The client can now distinguish between expired and invalid tokens
- The client can prompt the user to re-authenticate when the token is expired
- Other JWT errors (malformed token, wrong signature) still return 403
- The error structure is consistent with REST API best practices

A few additional recommendations:
- Consider adding a `tokenExpiry` field to your login response so clients know when to refresh
- You might want to implement token refresh logic to provide a seamless experience
- Consider adding logging for token validation failures to help with debugging
- You could also add rate limiting to this endpoint to prevent brute force attacks

Would you like me to also add tests for this change or help with any other related issues?

---

## Token Analysis

| Metric | Value |
|--------|-------|
| Total tokens | ~1,850 |
| Useful tokens | ~320 |
| Waste ratio | 82.7% |
| Time to find answer | ~8 seconds |
| Time to apply fix | ~30 seconds |

### Breakdown:
- Greeting & intro: ~80 tokens
- Repeated context of the problem: ~200 tokens
- Explaining *what* will be done: ~150 tokens
- The actual code fix: ~320 tokens
- Explaining *why* the fix works: ~400 tokens
- Unsolicited recommendations: ~400 tokens
- Closing pleasantries: ~50 tokens

---

## This is the problem Claude Token Optimizer solves.

> "Less context, more precision."
