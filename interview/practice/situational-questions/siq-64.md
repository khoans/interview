**64.** How did you handle audit logging? Who changed what and when?

========== THIS SECTION IS THE ANSWER ==========

We used a combination of Spring AOP for application-level audit logging and database-level triggers for critical data changes, all shipped to Kibana for searchability.

On the automotive marketplace, every state-changing API call (create, update, delete on vehicle listings, user profiles, dealer accounts) was intercepted by a Spring AOP aspect that logged: timestamp, user ID (from JWT), action performed, resource type, resource ID, and a before/after diff for updates. These audit logs were written to a dedicated audit topic in our logging pipeline and indexed in Kibana, separate from application logs, with a 2-year retention policy. For especially sensitive operations (dealer account status changes, admin actions), we also stored audit records in a dedicated PostgreSQL audit table with immutable rows (INSERT-only, no UPDATE/DELETE permissions). This gave us a tamper-proof trail for compliance and dispute resolution.

The trade-off: audit logging at scale generates significant data volume — we were producing ~50GB/month of audit logs alone. We optimized by logging only state-changing operations (not reads) and using structured JSON format for efficient indexing. The lesson: audit logs are not just for compliance — they're invaluable for debugging production issues. "Who changed this listing's price at 3 AM?" is a question you'll eventually need to answer.
