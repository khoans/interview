**58.** How did you protect your APIs from abuse? Rate limiting, throttling?

========== THIS SECTION IS THE ANSWER ==========

We used a multi-layer approach: AWS WAF at the edge, API Gateway throttling, and application-level rate limiting with ElastiCache Redis.

On the automotive marketplace, AWS WAF blocked known malicious IPs and patterns (SQL injection attempts, excessive request rates from single IPs) before traffic even reached our services. At the application level, we implemented rate limiting using a token bucket algorithm backed by ElastiCache Redis — each API key or user got a bucket with a configurable rate (e.g., 100 requests/minute for search, 20 requests/minute for write operations). When the bucket was empty, we returned HTTP 429 (Too Many Requests) with a Retry-After header. For unauthenticated endpoints (like vehicle search), we rate-limited by IP. For authenticated endpoints, by user ID. We also had separate, stricter limits for dealer batch upload APIs to prevent a single dealer from overwhelming the system.

The trade-off: rate limiting adds latency (Redis lookup on every request, ~1-2ms). We optimized by using a Lua script in Redis to do the check-and-decrement atomically, avoiding race conditions. The lesson: rate limiting is not just about abuse — it also protects your system from accidental DDoS by your own clients (e.g., a frontend bug causing infinite retry loops).
