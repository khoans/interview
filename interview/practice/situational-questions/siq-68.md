**68.** How did you handle session management? Session timeout, concurrent sessions, session hijacking?

========== THIS SECTION IS THE ANSWER ==========

We were primarily stateless (JWT), but for the admin dashboard that used sessions, we applied strict session controls via Spring Session with Redis.

On the automotive marketplace, our public APIs were stateless JWT-based, so traditional session management didn't apply there. But our internal admin dashboard used Spring Session backed by ElastiCache Redis. Configuration: session timeout was 30 minutes of inactivity, max concurrent sessions per user was 2 (the newest login would invalidate the oldest), and sessions were bound to the originating IP to mitigate session hijacking. Session cookies were marked HttpOnly (no JavaScript access), Secure (HTTPS only), and SameSite=Strict (no cross-site sending). We also regenerated the session ID on login (session fixation protection — Spring Security does this by default) and on privilege escalation (e.g., when an admin elevated to super-admin). Redis TTL handled automatic session expiration server-side.

The trade-off: IP-bound sessions break for users on mobile networks where IPs change frequently. We applied IP binding only for the admin dashboard (stable office IPs) and not for public users. The lesson: session security controls should match the use case — admin sessions need stricter controls than regular user sessions.
