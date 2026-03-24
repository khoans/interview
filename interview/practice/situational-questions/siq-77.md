**77.** How did you handle distributed transactions? Saga pattern? Two-phase commit?

========== THIS SECTION IS THE ANSWER ==========

We used the saga pattern with choreography via SQS/SNS for distributed transactions. Two-phase commit was never considered — it doesn't scale well in a microservices architecture serving 30M monthly users.

A concrete example: when a user submitted a lead (inquiry about a vehicle), multiple services needed to coordinate — the lead service created the lead record in PostgreSQL, the notification service sent email/SMS to the dealer via SNS, and the analytics service updated lead metrics in DynamoDB. Each service published events to SNS topics, and downstream services subscribed via SQS queues. If the notification service failed to send, it didn't roll back the lead — instead, it published a failure event, and a compensating Lambda function would queue the notification for retry and flag it in the dashboard. Each step was idempotent so reprocessing was safe.

Trade-off: sagas add complexity — you need to think about compensating transactions and eventual consistency. We accepted that a dealer might see a lead 30 seconds before getting the notification, which was a perfectly acceptable business trade-off versus the complexity of distributed locking.
