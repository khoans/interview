**81.** How did you optimize the performance of your application? Give a specific example with numbers.

========== THIS SECTION IS THE ANSWER ==========

The biggest performance win was optimizing our vehicle search results page. The initial P50 response time was around 1.8 seconds; we brought it down to 450ms.

The search aggregation service made sequential calls to Solr, pricing service, dealer service, and vehicle history. First, we parallelized independent downstream calls using CompletableFuture — this alone dropped response time from 1.8s to 900ms. Second, we added ElastiCache Redis caching for pricing data (5-minute TTL) and dealer info (15-minute TTL), which eliminated about 60% of downstream calls. That brought us to 550ms. Third, we optimized the Solr query itself — reduced the number of returned fields (fl parameter), added filter queries for common facets that hit Solr's filter cache, and pre-warmed the cache on deployment. That final tuning got us to 450ms P50 and 1.2s P99. We tracked all of this through New Relic dashboards with custom transaction segments for each downstream call.

Trade-off: caching added complexity around cache invalidation. When a dealer updated their pricing, we had to publish an SNS event to invalidate the Redis cache. Stale pricing for up to 5 minutes was acceptable for search results but not for the detail page (which always fetched fresh data).
