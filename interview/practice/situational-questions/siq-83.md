**83.** How did you optimize database queries in your project? EXPLAIN plan?

========== THIS SECTION IS THE ANSWER ==========

Yes, EXPLAIN ANALYZE was a regular tool in our workflow. We used it primarily for PostgreSQL queries in our satellite services (dealer info, lead management, vehicle history).

A specific example: our dealer leads report query was taking 12 seconds for dealers with 50,000+ leads. Running EXPLAIN ANALYZE showed a sequential scan on the leads table (2M rows) with a nested loop join to vehicles. The fix was a composite index on (dealer_id, created_at DESC) since every query filtered by dealer and sorted by date. We also added a covering index that included lead_status to avoid table lookups for the most common query. After the index, the query dropped from 12 seconds to 180ms. We also used Flyway migrations to manage all index changes consistently across environments. Beyond PostgreSQL, for Solr queries, we analyzed the debug output to identify slow filter queries and converted frequently used filters to cached filter queries. We set up a weekly slow query report from PostgreSQL's pg_stat_statements extension, alerting on any query exceeding 500ms average.

Trade-off: indexes speed up reads but slow down writes and consume storage. We were selective — only adding indexes for queries that appeared in our slow query report or New Relic's database tab, not preemptively.
