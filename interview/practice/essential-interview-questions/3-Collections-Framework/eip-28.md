28. What is the difference between fail-fast and fail-safe iterators?

**Fail-fast throws ConcurrentModificationException if the collection is modified during iteration; fail-safe continues without throwing.**

Fail-fast iterators (HashMap, ArrayList) detect structural changes mid-iteration and fail immediately. Fail-safe iterators (ConcurrentHashMap, CopyOnWriteArrayList) work on a snapshot or weakly consistent view — they don't see modifications made after iteration started.

**Trade-offs:**
- Fail-fast: Catches bugs early, but crashes on concurrent modification
- Fail-safe: No exceptions, but may iterate over stale data or miss recent changes
- Performance: Fail-safe often has memory/CPU overhead (snapshot copying)

**When to use:**
- Fail-fast: Single-threaded iteration, or when concurrent modification is a bug
- Fail-safe: When you need to iterate while other threads modify the collection

**Real-world example:**
We had a background job iterating over a user cache while the API layer updated it concurrently. Fail-fast iterator kept throwing ConcurrentModificationException — crashes in production. Switched to CopyOnWriteArrayList (fail-safe) — iteration works on a snapshot, updates happen on a new copy. Zero crashes, acceptable trade-off since reads were 100x more frequent than writes.

**Key insight:** Fail-fast is a debugging aid — it tells you "something's wrong here." Fail-safe is for legitimate concurrent access patterns. Don't use fail-safe to hide a design problem.
