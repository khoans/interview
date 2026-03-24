**17.** How do you handle data consistency across multiple services?

========== THIS SECTION IS THE ANSWER ==========

We used eventual consistency for most cross-service data flows, with a few exceptions where strong consistency was required.

The primary pattern was event-driven updates via SNS/SQS. When a dealer updated a listing in the dealer service (PostgreSQL), it published an event to an SNS topic. The search indexer consumed that event and updated Solr, the pricing service recalculated values, and the analytics pipeline recorded the change. Each consumer processed independently, so there was a window (usually under 5 seconds) where data could be inconsistent — a user might see the old price in search results but the new price on the detail page.

For cases where consistency mattered more — like when a user saved a vehicle and expected to see it immediately in their saved list — we used DynamoDB with strong read consistency. The save operation wrote directly to DynamoDB, and the read used ConsistentRead=true to avoid stale data.

We never used distributed transactions (2PC/XA) across services. The complexity and performance cost weren't worth it for our use cases. Instead, we relied on idempotent operations and SQS's at-least-once delivery with deduplication.

**Trade-off:** Eventual consistency is simpler and more scalable, but it requires the product team to accept that users might see slightly stale data. The key is communicating these windows to stakeholders and ensuring the user experience degrades gracefully, not confusingly.
