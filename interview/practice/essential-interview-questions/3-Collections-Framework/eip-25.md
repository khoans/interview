25. What is the difference between HashMap and ConcurrentHashMap?

**HashMap is not thread-safe; ConcurrentHashMap is.**

HashMap allows concurrent reads but fails catastrophically under concurrent writes — data corruption, infinite loops during rehashing. ConcurrentHashMap uses lock striping (or CAS operations in Java 8+) to allow safe concurrent access without locking the entire map.

**Trade-offs:**
- HashMap: Faster single-threaded, zero synchronization overhead
- ConcurrentHashMap: ~3-5x slower writes due to synchronization, but safe concurrent access
- ConcurrentHashMap doesn't allow null keys/values (HashMap does) — prevents ambiguity between "no mapping" and "null value"

**When to use:**
- HashMap: Single-threaded context, or external synchronization already handled
- ConcurrentHashMap: Multiple threads reading/writing without external locks

**Real-world example:**
We used ConcurrentHashMap for a request cache in our REST API — multiple threads handling concurrent user requests, caching computed results. HashMap would've caused race conditions. For local, method-level caching (single-threaded scope), we stuck with HashMap — no need for the synchronization overhead.

**Key insight:** Don't use ConcurrentHashMap "just in case." If you control the access pattern and it's single-threaded, HashMap is fine. But if multiple threads touch it, ConcurrentHashMap prevents subtle, hard-to-debug concurrency issues.
