**35. Distributed Tracing**
What is distributed tracing? Why is it important in microservices? How do tools like Jaeger or Zipkin work?

========== THIS SECTION IS THE ANSWER ==========

Distributed tracing follows a single request as it travels across multiple services. Each service adds a "span" (its portion of work) to a shared "trace" identified by a correlation ID. You can see the entire request journey: which services were called, in what order, and how long each took.

In our project, when a user searched for a car, the request hit: API Gateway → Search Aggregation Service → Solr → Inventory Service → Pricing Service. With New Relic's distributed tracing, we could see that a slow search was caused by the Pricing Service taking 2 seconds on a specific database query — something we'd never find from just looking at the search service logs alone.

The trade-off: tracing adds overhead — every request gets instrumented with trace context propagated in HTTP headers. For high-throughput services, we sampled traces (captured 10% of requests) rather than tracing everything. The key is: 100% tracing for errors, sampled tracing for successful requests.
