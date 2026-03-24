**19.** How do you handle shared data between services? Do you duplicate data or use a shared database?

========== THIS SECTION IS THE ANSWER ==========

We avoided shared databases. Each service owned its data store — the search service owned Solr, the dealer service owned its PostgreSQL schema, user preferences lived in DynamoDB. This gave us independent deployability and the freedom to choose the right storage technology per service.

We handled shared data through data duplication with event-driven sync. For example, the pricing service needed some dealer information to calculate prices. Rather than querying the dealer service at runtime (adding latency and coupling), the dealer service published events via SNS when dealer data changed. The pricing service consumed those events and maintained a local copy of the dealer attributes it needed in its own database.

For the search index, Solr contained denormalized data from multiple services — vehicle details, pricing, dealer info — all flattened into a single search document. The search indexer listened to events from all upstream services and rebuilt the relevant parts of the Solr document.

In a few cases where we needed real-time data that couldn't tolerate staleness, the aggregation service made synchronous REST calls to the owning service. But we kept these to a minimum because each synchronous call added latency and a failure point.

**Trade-off:** Data duplication means you're maintaining copies, which requires careful event handling and idempotent consumers. We had a few incidents where an event was lost or delayed, causing stale data in the search index. The fix was implementing dead letter queues and periodic full-sync jobs as a safety net. Duplication is extra work, but it's far better than a shared database that couples all your services together.
