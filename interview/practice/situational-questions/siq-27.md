**27.** Have you ever had a deadlock in your database? How did you detect and resolve it?

========== THIS SECTION IS THE ANSWER ==========

Yes. We had a deadlock in PostgreSQL in the dealer service when two concurrent requests tried to update the same dealer's listing inventory. Transaction A updated listing X then listing Y; Transaction B updated listing Y then listing X. Classic lock ordering issue.

Detection: PostgreSQL automatically detected the deadlock and killed one transaction with a "deadlock detected" error. We caught it in Kibana logs — the Spring Boot service logged the PSQLException. We also set up a CloudWatch alarm on the log pattern "deadlock detected" so we'd be alerted immediately.

Investigation: I checked pg_stat_activity and PostgreSQL's log_lock_waits setting to see the lock contention patterns. The root cause was a bulk update API that updated multiple listings in a single transaction, but the order of updates depended on the request payload — different requests could update the same listings in different orders.

Fix: I enforced a consistent lock ordering by sorting the listing IDs before updating them within the transaction. So both transactions would always lock listing X first, then listing Y. This eliminated the circular wait condition. I also reduced the transaction scope — instead of updating all listings in one big transaction, I batched them into smaller groups of 10, which reduced lock hold time.

**Lesson:** Deadlocks are usually a design problem, not a database problem. Consistent lock ordering and smaller transaction scopes prevent most deadlocks. Don't just catch and retry — fix the root cause.
