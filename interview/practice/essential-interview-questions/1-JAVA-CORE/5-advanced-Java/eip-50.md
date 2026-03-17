50. What is ThreadLocal?

**ThreadLocal provides thread-local variables — each thread accessing the variable gets its own independently initialized copy. It's the opposite of sharing.**

ThreadLocal solves the problem of making a variable thread-safe without synchronization. Instead of multiple threads competing for one variable, each thread has its own instance.

**How it works:**
- Each thread has an internal ThreadLocalMap
- When you call `get()`, it returns the value specific to that thread
- When you call `set()`, it stores in that thread's map
- Threads never see each other's values

**Trade-offs:**
- Memory: One copy per thread — 100 threads = 100 copies
- Thread pools: Values persist across task executions — must call `remove()` or leak
- Use cases: User context, transaction IDs, database connections — data tied to request lifecycle
- Not for: Sharing data between threads — defeats the purpose

**Real-world example:**
We had a multi-tenant application where every service method needed the tenant ID. Originally passed as parameter — 40% of methods had `String tenantId` parameter. Refactored to ThreadLocal — set once in request filter, read anywhere. Cleaned up method signatures. But we forgot `remove()` — thread pool reused threads with stale tenant IDs. User A's data leaked to User B. Fixed with try-finally: `set()` in filter, `remove()` in finally block. Zero leaks after that.

**Key insight:** ThreadLocal is powerful but dangerous. Use it for data that's truly request-scoped — user context, correlation IDs, locale settings. Always call `remove()` in a finally block, especially with thread pools. If you find yourself sharing ThreadLocal values between threads, you're using it wrong — use a concurrent collection instead.
