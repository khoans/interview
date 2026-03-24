**12.** Why did your team choose microservices (or monolith)? What were the trade-offs?

========== THIS SECTION IS THE ANSWER ==========

We actually started as a monolith and migrated to microservices. The original system was a large Spring Framework monolith that handled everything — search, vehicle details, pricing, dealer data. As the platform grew to 30M users/month with multiple teams working on different features, the monolith became a deployment bottleneck. A single change in the pricing logic required redeploying the entire application, and a bug in one module could bring down the whole system.

I was directly involved in the decoupling effort. We extracted domain-specific services — search aggregation, pricing, dealer management — each with its own data store (PostgreSQL or DynamoDB depending on the access pattern). Services communicated via REST for synchronous calls and SQS/SNS for async events like listing updates.

**Trade-off:** Microservices gave us independent deployability and fault isolation, but the operational complexity increased significantly. We now had to manage distributed tracing across services in New Relic, handle network failures between services, and deal with data consistency challenges that simply didn't exist in the monolith. Debugging a single user request that touched 5 services was much harder than reading a stack trace in one application. If your team is small and your domain is simple, a well-structured monolith is often the better starting point.
