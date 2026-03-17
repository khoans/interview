45. What is the synchronized keyword?

**Synchronized provides mutual exclusion — only one thread can execute a synchronized block or method at a time on the same object. It also guarantees visibility.**

Synchronized uses intrinsic locks (monitors). When a thread enters a synchronized block, it acquires the lock. Other threads trying to enter block until the lock is released.

**Trade-offs:**
- Mutual exclusion: Prevents race conditions, but causes contention under high concurrency
- Visibility: Changes made in synchronized block are visible to next thread acquiring the lock
- Performance: ~10x slower than volatile, much slower than unsynchronized code
- Deadlock risk: Multiple locks acquired in different orders can deadlock

**Real-world example:**
Our payment processing service had a shared `Map<String, Transaction> pendingTransactions`. Multiple threads updating it caused data corruption. Added synchronized to the update method — fixed corruption but throughput dropped 70% under load. Refactored to ConcurrentHashMap — kept thread safety, regained 90% of throughput. Kept synchronized only for compound operations like "check-then-act" that couldn't be done atomically.

**Key insight:** Synchronized is the blunt instrument of thread safety — reliable but expensive. Prefer concurrent collections (ConcurrentHashMap, BlockingQueue) and atomic classes (AtomicInteger) first. Use synchronized when you need to protect compound operations or legacy code.
