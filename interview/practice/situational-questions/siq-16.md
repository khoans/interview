**16.** What happens if one of your microservices goes down? How does the system handle it?

========== THIS SECTION IS THE ANSWER ==========

We designed for graceful degradation rather than total failure. The key mechanisms were multi-region deployment, circuit breakers, and fallback responses.

First, we deployed critical services across us-east-1 and us-west-2. Route 53 health checks would automatically route traffic away from an unhealthy region. If the pricing service went down in us-east-1, traffic shifted to us-west-2 within about 60 seconds.

Within a single region, we used circuit breakers (Resilience4j) in the aggregation service. If the pricing service started timing out, the circuit breaker would open after a threshold of failures, and the aggregation service would return cached pricing data from Elasticache instead of waiting. Users would see slightly stale prices (maybe 5-10 minutes old) rather than a broken page.

For async consumers, SQS provided natural resilience. If a consumer service was down, messages stayed in the queue (with configurable retention up to 14 days). Once the service recovered, it would process the backlog. Dead letter queues caught messages that failed repeatedly, and we had CloudWatch alarms to alert us.

New Relic APM and Kibana dashboards gave us real-time visibility. We had alerts for error rate spikes, latency increases, and instance health.

**Lesson:** The hardest part isn't handling the failure — it's deciding what the degraded experience looks like. You need to define fallback behavior for every downstream dependency upfront, not when the incident happens.
