## 15. What is the difference between String, StringBuilder, and StringBuffer?

String is immutable — every modification creates a new object. StringBuilder and StringBuffer are mutable — they modify the same object in place.

The key difference between StringBuilder and StringBuffer is thread-safety. StringBuffer is synchronized, StringBuilder is not. In practice, you almost always want StringBuilder because 99% of string building happens on a single thread.

**When to use each:**
- **String** — most of the time. Read-only, pass around, compare, store as keys. Immutability gives you thread-safety for free and enables string pooling
- **StringBuilder** — when you're building strings in a loop or concatenating many pieces. The classic example is assembling a JSON response, building SQL queries dynamically, or constructing log messages with multiple fields
- **StringBuffer** — rarely. Only when multiple threads are appending to the same buffer, which is an unusual design. In 15 years I've needed it maybe twice

**Trade-off:** StringBuffer pays a synchronization cost on every append call. In a high-throughput logging pipeline, switching from StringBuffer to StringBuilder cut our string-building latency by 30% — the lock contention was unnecessary because each thread had its own builder.

**One thing interviewers miss:** The compiler already optimizes simple concatenation like "a" + "b" + "c" into StringBuilder calls. So explicit StringBuilder only matters in loops where the compiler can't optimize — like appending inside a for-loop with hundreds of iterations.

**Bottom line:** Default to String. Use StringBuilder for performance-sensitive building. Forget StringBuffer exists unless you have a very specific multi-threaded append scenario.
