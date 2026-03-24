**72.** How did you handle third-party API failures? Retry? Circuit breaker? Fallback?

========== THIS SECTION IS THE ANSWER ==========

We used a combination of retries with exponential backoff and circuit breakers. Our vehicle search aggregation service called multiple downstream services — pricing, dealer info, vehicle history — and any of them could fail.

For example, the pricing service occasionally had latency spikes. We configured Resilience4j circuit breaker: after 5 consecutive failures within 10 seconds, the circuit opened for 30 seconds, during which we served cached prices from ElastiCache Redis. For retries, we used Spring Retry with exponential backoff (100ms, 200ms, 400ms) with max 3 attempts, but only for idempotent GET requests — never for writes. For the dealer inventory service, when it was down, we had a fallback that returned the last-known inventory count from DynamoDB instead of failing the entire search results page.

The lesson: fallbacks must be tested as thoroughly as the happy path. We once had a fallback that returned stale data for 2 hours because nobody noticed the circuit was open — we added New Relic alerts on circuit breaker state changes after that.
