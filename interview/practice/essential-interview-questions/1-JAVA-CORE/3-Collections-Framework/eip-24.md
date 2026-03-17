## 24. How does HashMap work internally?

**HashMap uses an array of buckets with linked lists (or trees in Java 8+) to handle collisions via chaining.**

When you put a key-value pair, HashMap hashes the key, uses modulo to find the bucket index, then stores the entry. Collisions — different keys hitting the same bucket — are resolved by chaining entries together.

**Key internals:**
- Load factor (default 0.75): When 75% full, HashMap resizes — rehashes all entries to a larger array
- Java 8+: Linked lists in buckets convert to red-black trees at 8+ entries (O(n) → O(log n) for lookups)
- hashCode() determines bucket, equals() handles collision resolution

**Trade-offs:**
- Good hashCode() distribution = fewer collisions = faster lookups (O(1))
- Bad hashCode() = many collisions = degrades to O(log n) or O(n)
- Resize is expensive — rehashes everything — so initial capacity matters

**Real-world example:**
We had a performance issue with a HashMap caching user sessions. Turns out the SessionKey class used only userId for hashCode() — all entries for the same user collided, creating long chains. Fixed by including sessionId and timestamp in hashCode(). Went from 50ms average lookup to sub-millisecond.

**Key insight:** HashMap's O(1) promise depends entirely on your hashCode() quality. A bad hashCode() turns your HashMap into a glorified linked list.
