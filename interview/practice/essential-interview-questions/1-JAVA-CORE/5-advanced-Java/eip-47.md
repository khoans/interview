47. What is a thread pool? Why use it?

**A thread pool is a pre-allocated collection of worker threads that execute submitted tasks. It avoids the cost of creating/destroying threads per task.**

Thread pools manage thread lifecycle for you. You submit tasks; the pool queues them and assigns to available threads. When a thread finishes, it picks up the next task instead of dying.

**Why use it:**
- Performance: Thread creation is expensive (~1ms per thread). Reusing threads amortizes this cost
- Resource control: Limits concurrent threads — prevents memory exhaustion under load
- Management: Centralized tuning — pool size, queue capacity, rejection policies

**Trade-offs:**
- Pool size: Too small = tasks queue up, too large = context switching overhead
- Queue type: Unbounded queue = memory risk under overload, bounded = task rejection
- Thread lifetime: Keep-alive time balances resource usage vs responsiveness

**Real-world example:**
Our REST API originally created a new thread per request. At 100 requests/second, thread creation consumed 10% of CPU. During spike (1000 req/sec), we ran out of memory — 2000+ threads alive. Switched to `Executors.newFixedThreadPool(50)` — capped at 50 threads, excess requests queued. CPU dropped 8%, zero OOM errors. Added monitoring — alert when queue depth exceeds 100, indicating we need to scale.

**Key insight:** Thread pools are about resource governance, not just performance. They prevent your application from consuming unlimited threads under load. Size based on workload — CPU-bound tasks need fewer threads (equal to cores), I/O-bound can use more (2-4x cores).
