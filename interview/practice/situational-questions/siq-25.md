**25.** Your database is growing too large and queries are getting slow. What steps do you take?

========== THIS SECTION IS THE ANSWER ==========

I follow a systematic approach rather than jumping to solutions.

Step 1: **Identify the slow queries.** I check New Relic database analytics and PostgreSQL's pg_stat_statements to find the top offenders by total execution time, not just per-query time. A query taking 50ms but called 100K times/day is worse than one taking 2 seconds called 10 times.

Step 2: **Optimize queries and indexes.** Run EXPLAIN ANALYZE, add missing indexes, rewrite inefficient JOINs. This usually gives the biggest bang for the least effort.

Step 3: **Archive old data.** In our vehicle_listing table, SOLD and DELETED listings older than 6 months accounted for 60% of the rows but were rarely queried. We moved them to an archive table and created a periodic archival job. This reduced the active table size significantly and improved index performance.

Step 4: **Table partitioning.** For tables that still grew too fast, we used PostgreSQL table partitioning by date range (e.g., monthly partitions on created_at). This let PostgreSQL prune irrelevant partitions during queries.

Step 5: **Read replicas.** We used RDS read replicas for reporting and analytics queries that didn't need real-time data, keeping the primary database focused on transactional workloads.

Step 6: **Caching.** Elasticache Redis for frequently accessed, rarely changing data like dealer profiles.

In our case, steps 2 and 3 alone solved 80% of the performance issues without any architectural changes.

**Lesson:** Don't jump to sharding or NoSQL migration before exhausting simpler solutions. Indexing, archival, and read replicas handle most growth problems at reasonable scale.
