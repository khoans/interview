**20.** What caching strategy do you use? When did caching cause problems instead of solving them?

========== THIS SECTION IS THE ANSWER ==========

We used multiple caching layers. CloudFront cached static assets and some API responses at the CDN edge. Elasticache (Redis) cached frequently accessed data like popular search filters, dealer metadata, and pricing lookups at the application layer. We also used in-memory caching with Caffeine in the Spring Boot aggregation service for short-lived data like configuration flags (TTL of 60 seconds).

The strategy was: CDN for static content, Redis for shared cross-instance data, in-memory for per-instance hot data. Cache invalidation was event-driven — when a dealer updated a listing, the SNS event triggered cache invalidation in Redis alongside the Solr index update.

**When caching caused problems:** We once cached search results aggressively in Redis with a 5-minute TTL to reduce Solr load. During a major dealer promotion event, dealers updated hundreds of listings with new prices, but users kept seeing stale prices from the cache for up to 5 minutes. Dealers complained because their promotions weren't visible immediately. We had to reduce the TTL to 30 seconds for pricing-sensitive queries and add event-driven cache invalidation for price changes. The problem was that we optimized for read performance without considering write frequency for that data type.

**Lesson:** Cache TTL should be based on business requirements, not just technical performance. Ask "how stale can this data be before a user or business partner notices?" before setting any TTL.
