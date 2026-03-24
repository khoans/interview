**79.** How did you handle message queue failures? What if a consumer crashes mid-processing?

========== THIS SECTION IS THE ANSWER ==========

We used SQS with dead letter queues (DLQ) and visibility timeouts to handle consumer failures gracefully.

In our vehicle listing pipeline, when a dealer updated a listing, the event went to SQS. The consumer had a visibility timeout of 5 minutes — if the consumer crashed mid-processing, the message became visible again after 5 minutes and another consumer instance picked it up. After 3 failed attempts (maxReceiveCount), the message moved to a DLQ. We had a CloudWatch alarm on DLQ depth — if messages accumulated, our team got paged via PagerDuty. We also had a Lambda function that ran daily to inspect DLQ messages and attempt reprocessing. Each consumer was idempotent (using DynamoDB dedup), so reprocessing was safe. For critical flows like lead creation, we used SQS FIFO queues with message group IDs per dealer to maintain ordering within a dealership.

Trade-off: visibility timeout is a balancing act. Too short (30 seconds) and slow consumers cause duplicate processing. Too long (10 minutes) and genuine failures take too long to retry. We tuned to 5 minutes based on P99 processing time from New Relic — about 3x our P99.
