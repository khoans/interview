**8.** Tell me about a bug that took you the longest to find. How did you eventually solve it?

========== THIS SECTION IS THE ANSWER ==========

We had an intermittent bug where search results would randomly return stale data — users would see cars that were already sold. It happened maybe 1 in 200 requests, impossible to reproduce locally, and no errors in the logs.

I spent 3 days on it. First, I checked the obvious: cache TTLs, Solr replication lag, database sync. All looked fine. Then I added detailed request-level logging with correlation IDs and waited for it to happen again. When it did, I traced the request and found it was hitting a specific ECS task. That task's JVM had a memory issue — the Solr client was caching old responses in a local cache that wasn't invalidated properly when memory pressure was high.

The fix was simple: disable the Solr client's local result cache (we already had ElastiCache for caching). But finding it took 3 days because the symptom (stale data) was far from the cause (JVM memory pressure on one specific instance).

The lesson: intermittent bugs are usually infrastructure-related, not code-related. Add correlation IDs and per-instance logging — they're lifesavers.
