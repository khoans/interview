46. How do we share data between threads?

**Shared data between threads requires visibility and atomicity guarantees — achieved via synchronized, volatile, concurrent collections, or atomic classes.**

Without proper synchronization, threads see stale cached values. Java provides multiple tools depending on the access pattern.

**Main approaches:**
- `volatile`: Simple flags, single writes — visibility only, no atomicity
- `synchronized`: Compound operations, multiple variables — mutual exclusion + visibility
- `AtomicInteger`, `AtomicLong`: Counters, numeric state — lock-free atomic operations
- `ConcurrentHashMap`, `BlockingQueue`: Shared collections — thread-safe without external locking
- `ThreadLocal`: Opposite pattern — each thread gets its own copy, no sharing

**Trade-offs:**
- Performance: volatile > atomic classes > synchronized (~1x, ~2x, ~10x overhead)
- Complexity: atomic classes are simplest, synchronized requires careful lock management
- Contention: synchronized blocks under high concurrency, atomics use CAS (compare-and-swap)

**Real-world example:**
Our order counter was a `static int orderId` incremented by 5 threads. Got duplicate IDs — race condition. First fix: synchronized method — worked but became a bottleneck at 1000+ orders/second. Final solution: `AtomicInteger` — lock-free, 5x faster, no duplicates. For shared cache, used `ConcurrentHashMap` — multiple threads reading/writing without blocking each other.

**Key insight:** Choose the lightest tool that works. Counter? Use `AtomicInteger`. Shared map? `ConcurrentHashMap`. Simple flag? `volatile`. Only reach for `synchronized` when you need to protect multiple variables or compound "check-then-act" logic.
