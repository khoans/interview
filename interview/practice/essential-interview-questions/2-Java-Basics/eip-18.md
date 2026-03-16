## 18. What is the difference between final, finally, and finalize?

Three completely unrelated concepts that share a name. Only two of them matter in modern Java.

**final** — a compile-time constraint that prevents change. On a variable, the reference can't be reassigned. On a method, it can't be overridden. On a class, it can't be subclassed. This is the one you'll use daily. It signals intent — "this shouldn't change" — and enables compiler optimizations. We mark service classes final by default to prevent accidental inheritance hierarchies that nobody asked for.

**finally** — a block that executes after try/catch regardless of whether an exception occurred. Classic use case: closing database connections, releasing file handles, cleaning up resources. That said, in modern Java you should prefer try-with-resources over explicit finally blocks. It's cleaner and eliminates the risk of forgetting to close in the finally block itself. We had a connection pool exhaustion incident traced to a finally block that threw its own exception, swallowing the original and skipping the connection close.

**finalize** — deprecated since Java 9, removed in Java 18. It was a method called by the garbage collector before reclaiming an object. In practice, it was unreliable — you had no guarantee when or even if it would run. It also added GC overhead and could resurrect objects accidentally. Never use it. If you need cleanup logic, use try-with-resources with AutoCloseable, or a Cleaner as a safety net.

**Bottom line:** Use final liberally for immutability. Use try-with-resources instead of finally when possible. Forget finalize exists.
