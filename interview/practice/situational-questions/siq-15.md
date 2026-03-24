**15.** How do your services communicate with each other? REST, gRPC, message queue?

========== THIS SECTION IS THE ANSWER ==========

We used a mix of synchronous REST and asynchronous messaging with SQS/SNS, chosen based on the use case.

For real-time request-response flows — like the aggregation service calling the pricing service during a search request — we used REST over HTTP with Spring's RestTemplate (later WebClient). These calls needed to return data immediately to the user, so synchronous made sense. We added circuit breakers with Resilience4j and timeouts (typically 2-3 seconds) to prevent cascading failures.

For fire-and-forget or fan-out scenarios, we used SNS topics with SQS queues. For example, when a dealer updated a vehicle listing, an SNS notification fanned out to multiple SQS queues — one for the Solr search indexer, one for the pricing recalculation service, one for the analytics pipeline. Each consumer processed at its own pace independently. AWS Lambda handled lightweight event-driven tasks like image processing triggered by S3 events.

We didn't use gRPC. The team was experienced with REST, our payloads were JSON-based, and the inter-service latency within the same AWS VPC was already low enough that gRPC's binary protocol advantage didn't justify the added complexity.

**Trade-off:** REST is simple and debuggable (you can curl any endpoint), but you pay for serialization overhead and need to handle failures explicitly. If I were starting fresh with latency-sensitive internal calls, I'd evaluate gRPC for service-to-service communication while keeping REST for external APIs.
