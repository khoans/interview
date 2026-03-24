**21.** Tell me about a slow query you found in production. How did you identify and fix it?

========== THIS SECTION IS THE ANSWER ==========

New Relic APM flagged a PostgreSQL query in the dealer service that was averaging 1.2 seconds, spiking to 4+ seconds during peak hours. It was a query that joined the dealer table with the vehicle_listing table to fetch active listings for a dealer, filtered by status and sorted by update date.

I pulled the query from New Relic's slow transaction trace and ran EXPLAIN ANALYZE in our staging database. The problem was a sequential scan on vehicle_listing — the table had grown to 8 million rows, and while we had an index on dealer_id, the query also filtered on status and sorted by updated_at. PostgreSQL was doing an index scan on dealer_id, then filtering and sorting the results in memory.

The fix was a composite index: CREATE INDEX idx_listing_dealer_status_updated ON vehicle_listing(dealer_id, status, updated_at DESC). This covered the WHERE clause and the ORDER BY in a single index scan. After deploying, the query dropped from 1.2 seconds to 15 milliseconds.

I also noticed the query was being called in a loop — the aggregation service fetched dealer IDs from one query, then called this query per dealer. I refactored it to use a single IN clause with a batch of dealer IDs, reducing the number of database round-trips from N to 1.

**Lesson:** Always check both the query plan and the calling pattern. A perfectly indexed query called 50 times in a loop is still a performance problem.
