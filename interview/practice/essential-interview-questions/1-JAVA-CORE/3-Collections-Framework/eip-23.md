## 23. What is the difference between HashMap and Hashtable?

Short answer: Hashtable is a legacy class from Java 1.0 — don't use it. Use HashMap for single-threaded code, ConcurrentHashMap for multi-threaded.

**The three differences everyone lists:**

- **Synchronization:** Hashtable synchronizes every method. HashMap is not synchronized. That synchronization sounds safe, but it's the wrong kind — it locks the entire table on every operation, creating a massive bottleneck under contention.
- **Null handling:** HashMap allows one null key and multiple null values. Hashtable throws NullPointerException on null keys or values. In practice, null keys are a code smell anyway, but it's a behavioral difference.
- **Iteration:** Hashtable's Enumerator is not fail-fast. HashMap's Iterator is fail-fast — it throws ConcurrentModificationException if the map is modified during iteration. Fail-fast is actually the safer behavior because it surfaces bugs early rather than silently producing wrong results.

**Why Hashtable is obsolete:**

Its synchronization strategy is coarse-grained — one lock for the entire table. ConcurrentHashMap uses segment-level locking (pre-Java 8) or CAS operations with node-level locking (Java 8+), allowing multiple threads to read and write concurrently without blocking each other. We migrated a session store from a synchronized Hashtable to ConcurrentHashMap and throughput jumped 4x under 200 concurrent requests — same data, same logic, just better concurrency.

**If someone asks "but what if I need a synchronized HashMap?"** Use Collections.synchronizedMap() as a wrapper — it gives you the same coarse locking as Hashtable but with HashMap's API. Still, ConcurrentHashMap is almost always the better choice for real concurrent access.

**Bottom line:** HashMap for single-threaded, ConcurrentHashMap for multi-threaded. Hashtable exists only in legacy codebases and interview questions.
