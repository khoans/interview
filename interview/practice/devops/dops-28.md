**28. Serverless Architecture**
What is serverless computing (e.g., AWS Lambda)? What are the pros and cons compared to traditional server-based deployments?

========== THIS SECTION IS THE ANSWER ==========

Serverless means you write functions and the cloud provider handles scaling, provisioning, and infrastructure. You pay per invocation, not per running server. AWS Lambda, Google Cloud Functions, Azure Functions are the main offerings.

In our project, we used Lambda for event-driven tasks — processing SQS messages for analytics events, image resizing when dealers uploaded vehicle photos, and scheduled data cleanup jobs. These workloads were bursty (high volume during business hours, zero at night), so serverless saved ~60% cost compared to running dedicated servers 24/7.

The trade-off: cold starts add latency (our Java Lambdas took 3-5 seconds on first invocation), you're limited to 15-minute execution time, and debugging is harder. We kept our core search API on ECS (always-on, low latency) and used Lambda only for async, event-driven workloads where occasional cold starts didn't matter.
