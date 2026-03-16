## 14. Why is String immutable in Java?

**String is immutable for security, thread-safety, and performance.**

Once created, a String's value cannot change. Every "modification" creates a new String.

**Why this design:**

- **Security**: Method parameters can't be changed by the callee. File paths, SQL queries, network connections stay trustworthy.
- **Thread-safety**: No synchronization needed. Safe to share across threads without locks.
- **Performance**: Enables string pooling. Same literals share one instance in memory — reduces heap pressure.

**Trade-off:**
- Immutability costs memory and CPU for frequent modifications
- But 90% of String usage is read-only — optimize for the common case

**Real-world example:**
We had a log aggregation service processing 50K messages/second. Strings were passed through 8 pipeline stages. If String were mutable, any stage could corrupt data mid-pipeline. Immutability meant zero defensive copies, zero race conditions. When we switched to StringBuilder for concatenation-heavy paths, we cut allocations by 40% — but only after profiling proved it was a bottleneck.

**Bottom line:** Immutable by default, mutable when you explicitly opt-in (StringBuilder, StringBuffer).
