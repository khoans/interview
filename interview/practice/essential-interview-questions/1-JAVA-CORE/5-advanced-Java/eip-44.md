44. What is the volatile keyword?

**Volatile guarantees visibility — changes by one thread are immediately visible to other threads. It does not guarantee atomicity.**

Without volatile, threads cache variables locally — thread A modifies a value, thread B never sees it. Volatile forces reads/writes to go directly to main memory.

**Trade-offs:**
- Visibility: Guaranteed across threads, but only for the variable itself
- Atomicity: NOT provided — `count++` is still three operations (read, increment, write)
- Performance: Lighter than synchronized (~10x faster), but heavier than non-volatile
- Use cases: Flags, status indicators, single writes — not counters or compound operations

**Real-world example:**
We had a shutdown flag `boolean running = true` checked by worker threads. Without volatile, threads cached the value — they never saw when the main thread set it to false. Added volatile, workers stopped within milliseconds. But we made a mistake: used volatile for a `requestCount` counter. Got race conditions — `count++` isn't atomic. Replaced with AtomicInteger for counters, kept volatile for simple flags.

**Key insight:** Volatile solves "will other threads see my change?" Not "will my read-modify-write be atomic?" For flags and status — use volatile. For counters or compound operations — use AtomicInteger, synchronized, or locks.
