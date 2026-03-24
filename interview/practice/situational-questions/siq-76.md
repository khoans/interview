**76.** How did you handle race conditions in your application? Give a specific example.

========== THIS SECTION IS THE ANSWER ==========

We encountered race conditions mainly around inventory updates and used a combination of optimistic locking and distributed locks depending on the scenario.

Specific example: our dealer inventory service allowed dealers to update vehicle listings. Two dealers from the same dealership could update the same listing simultaneously. We used optimistic locking with a `@Version` column in PostgreSQL — if two concurrent updates hit the same row, the second one would get an `OptimisticLockException`, and we retried with the fresh data. For a more critical case — generating unique lead reference numbers — we used Redis distributed locks with Redisson (tryLock with 5-second timeout and 30-second lease). This ensured only one instance generated a reference number at a time across all ECS containers. We also used DynamoDB conditional writes for updating vehicle view counts, which is atomic by nature.

Lesson: optimistic locking works great for low-contention scenarios (most of our cases), but for high-contention hot keys, you need distributed locks or atomic operations. Start simple, escalate only when you see actual conflicts in production metrics.
