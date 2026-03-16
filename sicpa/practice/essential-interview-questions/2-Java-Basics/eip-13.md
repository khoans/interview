## 13. What is the contract between hashCode() and equals()?

**Answer:**

If two objects are equal by `equals()`, they must have the same `hashCode`. The reverse isn't required — same hashCode doesn't mean equal.

This contract exists because hash-based collections (HashMap, HashSet) use hashCode for bucketing, then equals for actual comparison.

Trade-off: You can have all objects return the same hashCode (technically valid), but HashMap degrades to O(n) lookup. Good hashCode distributes objects evenly across buckets.

Real-world issue: We inherited a system where a developer overridden `equals()` but not `hashCode()` on an Entity class. Worked fine in unit tests, but in production the HashSet kept duplicate entries. Caused data inconsistencies that took days to trace back.

Rule of thumb: Always override both together. IDEs can generate both correctly. For JPA entities, use business keys (natural identifiers), not database IDs, since IDs are null before persist.

**If two objects are equal by equals(), they must have the same hashCode().**

The reverse isn't required — different objects can share the same hash code (collision).

**Why this matters:**
- Hash-based collections (HashMap, HashSet) use hashCode() to find the bucket
- Then use equals() to find the exact match within that bucket
- Break the contract → your objects vanish from collections

**Trade-off:**
- Computing complex hashCode() costs CPU but gives better distribution
- Simple hashCode() is fast but causes collisions → degrades to O(n) lookups

**Real-world example:**
A developer overrode equals() for an `Order` class but not hashCode(). Orders started "disappearing" from a HashSet — we lost 15% of high-value orders in a 3-day period. Root cause: equal objects had different hash codes, so HashSet treated them as distinct. Fix took 20 minutes; post-mortem took 2 hours.

**Rule of thumb:** Override both together. IDE generates them in one action — no excuse to skip.
