**69.** How did you handle token refresh? What happens when a JWT expires during a user action?

========== THIS SECTION IS THE ANSWER ==========

Short-lived access tokens (15 min) with a longer-lived refresh token, and a silent refresh mechanism on the frontend to avoid disrupting user actions.

On the automotive marketplace, our auth flow used two tokens: an access JWT (15-minute expiry, sent in the Authorization header) and a refresh token (7-day expiry, stored in an HTTP-only secure cookie). The Next.js frontend intercepted 401 responses via an Axios interceptor — when an access token expired mid-action, the interceptor automatically called the /auth/refresh endpoint with the refresh cookie, got a new access token, and retried the original request transparently. The user never saw an error. If the refresh token was also expired, we redirected to login. On the backend, the refresh endpoint validated the refresh token against a whitelist in ElastiCache Redis (allowing us to revoke refresh tokens on logout or password change), issued a new access token, and rotated the refresh token (one-time use) to prevent token replay attacks.

The trade-off: refresh token rotation means every refresh invalidates the old token. If two tabs try to refresh simultaneously, one will fail. We handled this with a mutex on the frontend — only one refresh request at a time, other requests queue behind it. The lesson: token refresh is where most JWT implementations break. Test the edge cases: concurrent tabs, slow networks, expired refresh tokens, and revoked accounts.
