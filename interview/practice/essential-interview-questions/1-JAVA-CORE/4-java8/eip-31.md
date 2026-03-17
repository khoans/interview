31. What are the benefits of Java 8?

**Java 8 introduced functional programming (lambdas, streams) and better date/time API — reducing boilerplate and enabling more declarative code.**

The big wins: lambdas for concise anonymous functions, Stream API for collection operations, Optional for null safety, and the new Date/Time API (java.time) that's immutable and thread-safe.

**Trade-offs:**
- Streams: More readable, but can be slower than loops for simple operations (~10-20% overhead)
- Lambdas: Cleaner code, but harder to debug (stack traces show lambda$0, not method names)
- Optional: Reduces NullPointerException, but misuse can spread null checks instead of eliminating them

**Real-world example:**
We migrated a legacy payment processing module from Java 7 to 8. Replaced 200 lines of iterator-based filtering/transforming with 20 lines of stream pipelines. The Date/Time API fixed timezone bugs — old Date class was mutable and timezone-ambiguous, LocalDateTime/ZonedDateTime are explicit. Cut bug reports by 40%.

**Key insight:** Java 8 isn't just syntax sugar — it's a mindset shift from imperative to declarative. But don't overuse streams. A simple for-loop is still faster and clearer for basic iteration.
