**22.** How do you decide which columns to index? Have you ever created an index that didn't help?

========== THIS SECTION IS THE ANSWER ==========

My approach: index columns that appear in WHERE clauses, JOIN conditions, and ORDER BY of frequent queries. I check query patterns in New Relic's database analytics to identify the most-called and slowest queries first, then use EXPLAIN ANALYZE to verify the index will actually be used.

For composite indexes, column order matters — I put the most selective (highest cardinality) column first, then follow the query's filter order. I also consider covering indexes that include all columns needed by the query to avoid table lookups.

**An index that didn't help:** I once created an index on a status column in the vehicle_listing table. The column had only 4 possible values (ACTIVE, PENDING, SOLD, DELETED), and about 80% of rows were ACTIVE. When querying WHERE status = 'ACTIVE', PostgreSQL's query planner correctly ignored the index and did a sequential scan instead — because reading 80% of the table through an index is actually slower than a sequential scan due to random I/O. The index only helped for the minority values (WHERE status = 'SOLD'), which was rarely queried.

I replaced it with a partial index: CREATE INDEX idx_listing_pending ON vehicle_listing(id) WHERE status = 'PENDING'. This was tiny and lightning-fast for the specific query that needed it.

**Lesson:** Low-cardinality columns are usually poor index candidates unless combined in a composite index or used as a partial index. Always validate with EXPLAIN ANALYZE — don't assume adding an index will improve things.
