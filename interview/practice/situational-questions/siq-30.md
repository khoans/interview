**30.** Your application suddenly starts getting connection pool exhaustion errors. How do you troubleshoot?

========== THIS SECTION IS THE ANSWER ==========

This happened to us in the aggregation service during a traffic spike. HikariCP started logging "Connection is not available, request timed out after 30000ms."

Step 1: **Check metrics immediately.** In New Relic, I looked at active connections, pending connection requests, and database response times. The pool was maxed at 10 connections, all busy, with a growing queue of waiting threads.

Step 2: **Identify the bottleneck.** Database response times had spiked from 20ms to 3 seconds. That meant each connection was held 150x longer than normal, so 10 connections couldn't serve the usual throughput.

Step 3: **Find the root cause.** I checked PostgreSQL's pg_stat_activity and found a long-running query from a new reporting feature that a teammate had deployed that morning. It was doing a full table scan on vehicle_listing (8M rows) without proper indexes, and it was running inside a @Transactional method, holding a connection for 15+ seconds per request.

Step 4: **Immediate fix.** We disabled the reporting endpoint via feature flag. Connection pool recovered within seconds.

Step 5: **Permanent fix.** Added a proper index for the reporting query, set a statement timeout of 5 seconds in HikariCP configuration, moved reporting queries to the read replica instead of the primary database, and increased the pool size from 10 to 20 with proper monitoring.

I also added HikariCP metrics to our New Relic dashboard — active connections, idle connections, pending threads, and connection acquisition time — so we'd get alerted before exhaustion happened.

**Lesson:** Connection pool exhaustion is almost never solved by just increasing pool size. Find why connections are being held too long. Common culprits: missing indexes, long transactions, and external HTTP calls made inside a database transaction.
