**78.** How did you handle idempotency in your APIs? Why is it important?

========== THIS SECTION IS THE ANSWER ==========

Idempotency is critical when you have retries, message queues, and network failures — without it, you get duplicate leads, double notifications, or corrupted data. We implemented it at multiple levels.

For our lead submission API, we required a client-generated Idempotency-Key header (UUID). On the server side, we stored the key in Redis with a 24-hour TTL along with the response. If a duplicate request came in with the same key, we returned the cached response without re-processing. For SQS message consumers, we used DynamoDB as a deduplication store — before processing a message, we did a conditional put with the message ID. If it already existed, we skipped processing. This was essential because SQS has at-least-once delivery, so our dealer notification consumer could receive the same lead event twice. For database operations, we used INSERT ON CONFLICT DO NOTHING in PostgreSQL for cases like vehicle view tracking.

Lesson: idempotency isn't free — it requires extra storage (Redis/DynamoDB) and adds latency for the lookup. But in a distributed system with eventual consistency, it's not optional. The cost of duplicate processing (e.g., sending a dealer the same lead email twice) is far worse than a few milliseconds of overhead.
