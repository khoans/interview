**84.** How did you reduce API response time? What was the before/after?

========== THIS SECTION IS THE ANSWER ==========

Our search results API went from 1.8 seconds P50 down to 450ms P50 through a series of targeted optimizations.

The biggest wins in order: (1) Parallelized downstream service calls using CompletableFuture — the aggregation service was calling Solr, pricing, dealer, and history services sequentially. Making them parallel cut response time nearly in half, from 1.8s to 900ms. (2) Added Redis caching for pricing and dealer data, eliminating 60% of downstream calls — down to 550ms. (3) Optimized Solr query: reduced returned fields, used filter queries for cacheable facets, and tuned the Solr JVM heap — down to 450ms. (4) On the Next.js frontend side, we implemented server-side rendering with streaming and reduced JavaScript bundle size from 380KB to 210KB using dynamic imports. We also added response compression (gzip) at the ALB level, reducing payload size by about 70%. All metrics were tracked through New Relic transaction traces, which showed exactly which segment of the request was slow.

Lesson: always measure before optimizing. We initially assumed the database was the bottleneck, but New Relic showed it was the sequential HTTP calls. Without tracing, we would have wasted time optimizing the wrong thing.
