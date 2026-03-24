**52.** How did you implement authentication in your project? JWT, session, OAuth2? Why that choice?

========== THIS SECTION IS THE ANSWER ==========

We used OAuth2 with JWT tokens, implemented via Spring Security. The choice was driven by our microservices architecture — stateless tokens scale better than server-side sessions across 10+ services.

On the automotive marketplace, the authentication flow worked like this: users authenticated through our identity provider, received a JWT access token (short-lived, 15 minutes) and a refresh token (longer-lived, stored in an HTTP-only secure cookie). Each microservice validated the JWT independently using the public key — no need to call the auth server on every request, which was critical at 30M users/month. The JWT contained user ID, roles, and a few claims. We chose JWT over session-based auth because with services running across us-east-1 and us-west-2, sharing session state would have required sticky sessions or a distributed session store like Redis, adding latency and a single point of failure.

The trade-off: JWTs can't be revoked instantly — if a token is compromised, it's valid until expiry. We mitigated this with short expiry times (15 min) and a token blacklist in ElastiCache Redis for critical cases (account compromise, forced logout). Session-based auth would have been simpler for a monolith, but wrong for our distributed setup.
