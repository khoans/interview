48. What is the ExecutorService framework?

**ExecutorService is Java's high-level framework for managing thread pools and asynchronous task execution. It decouples task submission from execution.**

Instead of manually creating threads, you submit tasks (Runnable or Callable) to an ExecutorService. It handles thread lifecycle, pooling, and task scheduling. You get a Future back to retrieve results.

**Key components:**
- ExecutorService: Manages thread pool and task lifecycle
- ThreadPoolExecutor: Core implementation with configurable pool size, queue, rejection policies
- Future: Represents pending result from a Callable task
- Executors: Factory class with pre-built pools (fixed, cached, scheduled)

**Trade-offs:**
- Flexibility: Highly configurable, but complex — 7 constructor parameters for ThreadPoolExecutor
- Queue types: Unbounded (LinkedBlockingQueue) = memory risk, bounded (ArrayBlockingQueue) = rejection risk
- Rejection policies: Abort (throw exception), CallerRuns (execute in submitting thread), Discard, DiscardOldest
- Shutdown: shutdown() waits for tasks, shutdownNow() interrupts — choosing wrong causes hangs or data loss

**Real-world example:**
We had a batch email notification system originally using `new Thread()` per email. At 10,000 users, we created 10,000 threads — OOM crash. Refactored to ExecutorService with fixed pool of 20 threads. Tasks queued, executed orderly. Added CompletionService to track progress — could report "8,000 of 10,000 sent". Reduced memory 90%, completed in same time. One gotcha: forgot to call shutdown() — application wouldn't terminate. Added shutdown hook, fixed it.

**Key insight:** ExecutorService is the foundation of concurrent Java applications. But don't use `Executors.newFixedThreadPool()` blindly — it uses unbounded queues, can OOM under load. Create ThreadPoolExecutor directly with bounded queues and explicit rejection policies. Always shutdown properly — use try-finally or shutdown hooks.
