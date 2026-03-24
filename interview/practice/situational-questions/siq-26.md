**26.** How do you handle transactions across multiple services?

========== THIS SECTION IS THE ANSWER ==========

We avoided distributed transactions entirely. Instead, we used the Saga pattern with choreography through SNS/SQS events.

A real example: when a user submitted a "contact dealer" request, multiple services needed to coordinate — the lead service created a lead record, the notification service sent an email to the dealer, and the analytics service tracked the conversion event. Rather than a distributed transaction, the lead service created the record in its own database and published an event to SNS. The notification and analytics services consumed the event independently from their SQS queues.

For failure handling, each service implemented compensating actions. If the notification service failed to send the email, it retried from the SQS queue (with exponential backoff, up to 3 retries). If it still failed, the message went to a dead letter queue, and a CloudWatch alarm notified the team. The lead record itself remained valid — we didn't roll it back just because the email failed.

For cases where ordering mattered, we used SQS FIFO queues with message group IDs to ensure events for the same entity were processed in order.

**Trade-off:** The Saga pattern means you accept eventual consistency and need to handle partial failures gracefully. It's more work upfront — you need compensating logic, idempotent handlers, and good monitoring. But it scales far better than 2PC (two-phase commit), which would lock resources across services and create a single point of failure at the transaction coordinator. For our scale of 30M users/month, 2PC was simply not an option.
