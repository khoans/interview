**13.** How does your system handle 30 million users per month? What are the bottlenecks?

========== THIS SECTION IS THE ANSWER ==========

Several layers work together to handle that scale. First, CloudFront CDN caches static assets and some API responses close to users. Behind that, Application Load Balancers distribute traffic across multiple ECS instances of each service. We deploy in two AWS regions — us-east-1 and us-west-2 — with Route 53 doing latency-based routing, so a user in California hits the west coast cluster.

For search specifically, Apache Solr handles the heavy lifting. Solr clusters are replicated and sharded, so search queries are distributed. DynamoDB handles high-throughput key-value lookups like saved searches and user preferences with single-digit millisecond latency, which offloads the relational database. Async processing via SQS decouples write-heavy operations — when a dealer updates a listing, it goes to a queue rather than blocking the request.

**Bottleneck:** The main one was our aggregation service. Every search request funneled through it to orchestrate calls to Solr, pricing, and dealer services. During peak traffic (like holiday sales events), this service was the first to feel pressure. We addressed it by adding Elasticache (Redis) for frequently accessed data like popular search filters and dealer info, and by tuning connection pools. The lesson: an orchestration layer is convenient but becomes a single point of contention at scale — keep it as thin as possible.
