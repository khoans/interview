**82.** How did you handle caching? What did you cache and what invalidation strategy did you use?

========== THIS SECTION IS THE ANSWER ==========

We used a multi-layer caching strategy: Caffeine for local in-memory cache and ElastiCache Redis for distributed cache.

In our search aggregation service, we cached Solr search results in Redis with a 2-minute TTL for the top 1,000 most frequent queries — this covered roughly 40% of all traffic. Dealer metadata (name, address, ratings) was cached in Redis with a 15-minute TTL since it rarely changed. For invalidation, we used event-driven cache busting: when a dealer updated their profile, the dealer service published an SNS event, and our service's SQS consumer evicted that specific dealer key from Redis. Vehicle pricing had a 5-minute TTL with no active invalidation — the short TTL was sufficient since search results didn't need real-time pricing accuracy. For local cache, Caffeine held reference data (vehicle makes/models, state lists) with a 1-hour TTL, reducing Redis round-trips. We monitored cache hit rates in New Relic — our Redis hit rate was around 72%, and local cache was above 95%.

Lesson: don't cache everything. We initially cached vehicle detail pages and ran into stale data complaints from dealers who updated listings and didn't see changes. We removed that cache and only cached data where staleness was acceptable (search results, aggregated counts).
