**28.** When would you choose NoSQL over SQL? Give a real example from your experience.

========== THIS SECTION IS THE ANSWER ==========

I choose NoSQL when the access pattern is primarily key-value lookups, the data has variable schema, or the read/write throughput requirements exceed what a single relational instance can handle cost-effectively.

Real example: In our automotive marketplace, we used DynamoDB for user saved searches and preferences. The access pattern was simple — get all saved searches for user X, save a new search for user X. The data schema varied per user (different filter combinations, different number of saved searches). And with 30M users/month, the read throughput for this feature was extremely high.

DynamoDB gave us single-digit millisecond reads at any scale with predictable pricing (pay per request). The partition key was user_id, sort key was saved_search_id. Auto-scaling handled traffic spikes without any operational overhead. We would have needed a large PostgreSQL instance with read replicas and careful connection pool tuning to handle the same throughput, at higher cost and more operational burden.

Meanwhile, we kept all transactional data — dealer information, listing details, pricing — in PostgreSQL because that data had complex relationships, required JOINs, and needed ACID transactions (e.g., when updating a dealer's listing count alongside the listing record).

**Trade-off:** DynamoDB's simplicity comes with constraints. No ad-hoc queries, no JOINs, and you must design your table around access patterns upfront. We once needed to query saved searches by vehicle model across all users for analytics — impossible efficiently in DynamoDB. We had to stream changes to an analytics pipeline using DynamoDB Streams. Choose your data store based on how you'll read the data, not just how you'll write it.
