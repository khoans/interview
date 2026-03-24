**74.** How did you handle memory leaks in your Java application? How did you detect them?

========== THIS SECTION IS THE ANSWER ==========

We detected memory issues primarily through New Relic JVM monitoring and heap dump analysis. We had alerts set for when heap usage consistently stayed above 85% after garbage collection.

One specific case: our search aggregation service started getting OOM kills on ECS after about 3 days of running. New Relic showed a sawtooth pattern with the baseline creeping up. We took a heap dump using `jmap` and analyzed it with Eclipse MAT. The root cause was a ConcurrentHashMap used as an in-memory cache for Solr query results that had no eviction policy — it just grew forever. The fix was replacing it with Caffeine cache with a max size of 10,000 entries and 5-minute TTL, and for larger datasets, offloading to ElastiCache Redis. After the fix, heap stabilized at around 60% post-GC.

Lesson learned: never use unbounded collections as caches. Even a simple HashMap with no eviction becomes a memory leak under sustained traffic. We added a code review checklist item for this.
