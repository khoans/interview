**40.** How do you handle logging across multiple services?

========== THIS SECTION IS THE ANSWER ==========

We used centralized structured logging with correlation IDs to trace requests across services.

**Structured logging:** Every service used SLF4J with Logback, configured to output JSON format. Each log entry included: timestamp, service name, log level, correlation ID, and the message. No free-text log.info("User " + userId + " searched for " + query) — instead, we used structured fields: log.info("Search executed", Map.of("userId", userId, "query", query, "resultCount", results.size())).

**Correlation ID propagation:** The aggregation service generated a UUID correlation ID for each incoming request and passed it to downstream services via an HTTP header (X-Correlation-ID). A Spring interceptor on each service extracted this header and added it to the MDC (Mapped Diagnostic Context), so every log line automatically included it. For async flows via SQS, the correlation ID was included as a message attribute.

**Centralized aggregation:** All services shipped logs to Elasticsearch via Filebeat agents running on each ECS instance. Kibana provided the search interface. I could search for a specific correlation ID and see the entire request lifecycle — from the aggregation service, through the pricing call, the Solr query, back to the response — all in chronological order across services.

**Log levels in production:** We ran at INFO level by default. DEBUG was enabled per-service via Parameter Store when troubleshooting specific issues, then turned back off to avoid log volume costs.

**Retention:** 30 days in Elasticsearch for searchability, 90 days in S3 for compliance. Logs older than 30 days required querying from S3 directly, which was slower but rarely needed.

**Lesson:** The correlation ID is the single most valuable thing in distributed logging. Without it, debugging a user issue across 5 services is like finding a needle in a haystack. Implement it on day one, not after the first production incident.
