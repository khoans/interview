**44.** You found a critical bug in production at 11 PM. What do you do?

========== THIS SECTION IS THE ANSWER ==========

Triage severity, mitigate immediately, fix properly later. Don't panic-code a solution at midnight.

On the automotive marketplace, we once had an issue where a deployment to us-east-1 caused the search aggregation service to return empty results for certain vehicle makes. New Relic alerts fired around 10 PM — error rate spiked to 15%. I checked Kibana logs, identified the root cause was a malformed Solr filter query introduced in the latest release. Rather than writing a code fix at night, I rolled back the deployment via our GitHub Actions pipeline — the rollback took about 8 minutes and restored service. I posted an incident summary in Slack, tagged the on-call PM, and wrote up the root cause. Next morning, I fixed the query builder, added a regression test, and deployed through the normal pipeline with proper code review.

The lesson: your first job is to stop the bleeding (rollback, feature flag, disable endpoint), not to fix the code. A rushed fix at 11 PM has a high chance of introducing a second bug. Rollback fast, fix right in the morning.
