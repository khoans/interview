# Interview Answer: Describe the Architecture of Your Project

## Short Version (~60 seconds)

The system is a microservices architecture serving 30 million users per month across multiple brands like Autotrader and Kelley Blue Book.

On the frontend, we use a microsite architecture — Akamai routes traffic by URL path to different applications. The search pages use Next.js with a custom Node.js framework for SSR and middleware. Other pages use WordPress and Sitecore CMS. All frontends consume the same backend API and feed user behavior into a shared analytics pipeline.

On the backend, the core is the LSC — Listing Search Composition — a Spring Boot aggregation service. It doesn't own data itself; it orchestrates calls to Apache Solr for search and satellite services for listings, dealers, pricing, and personalization. Think of it as the gateway that assembles everything the frontend needs in a single response.

We also have serverless services on AWS Lambda — natural language search using Bedrock, pricing calculations, SEO metadata. For async work, we use SQS and SNS. Everything is deployed across two AWS regions for high availability, auto-scaling from 20 to 100 instances based on traffic.

---

## Longer Version (~2-3 minutes, if interviewer asks for more detail)

Let me walk through the architecture layer by layer.

**Edge layer:** Akamai sits at the edge doing URL-based routing. When a user hits autotrader.com/cars-for-sale, Akamai routes to our find-cars Next.js app. The homepage goes to WordPress, editorial content goes to Sitecore. The user sees one site, but under the hood it's multiple independent applications. This lets different teams deploy independently.

**Frontend layer:** Our search pages use Next.js wrapped in a custom Node.js framework called bonnet-next. It provides a middleware pipeline — each app builds its own plugins for caching headers, auth, request transformation. We leverage SSR for SEO. All frontend apps share a unified UI component library — we were actively migrating to a new accessibility-first library to meet US ADA compliance requirements.

**Backend layer:** The LSC is the heart of the system. When the frontend requests search results, the LSC queries Apache Solr for vehicle listings, then enriches the results by calling satellite services — listings service for details, pricing service for deal ratings, personalization engine for ranking based on user behavior. It aggregates everything into a single response. The LSC serves not just our frontend but also other internal teams across the department — it's a shared API.

**Serverless layer:** We built the natural language search as an AWS Lambda function calling AWS Bedrock. Users type "red SUV under 30k near Atlanta" and the Lambda translates that into structured Solr query parameters. We used Lambda because this feature is used sporadically — no point running a dedicated server for it. Same pattern for the pricing service and SEO metadata generation.

**Async layer:** For non-real-time work, we use SQS and SNS. Analytics events, inventory updates, cache invalidation — all event-driven. This decouples services so a slow analytics consumer doesn't affect search latency.

**Data layer:** PostgreSQL for relational data in satellite services, DynamoDB for high-throughput access patterns, ElastiCache Redis for caching, and Solr as the search engine.

**Infrastructure:** Everything runs on AWS Elastic Beanstalk with Docker on EC2. The LSC and main frontend auto-scale from 20 to 100 instances. Deployed across us-east-1 and us-west-2 with Route 53 health-check failover. CI/CD through GitHub Actions with automatic PR environments — every pull request gets its own deployable environment with a unique subdomain.

---

## Common Follow-Up Questions & Answers

**Q: Why did you choose this architecture?**
The system started as a monolith. As we grew to 200+ developers, we split it into microservices using the Strangler Fig pattern. The microsite frontend pattern lets each team own their pages end-to-end. The LSC aggregation pattern keeps the frontend simple — one API call instead of calling 5 services directly.

**Q: What's the biggest bottleneck?**
The LSC is both the strength and the bottleneck. Every frontend request goes through it. We mitigate this with aggressive caching (Redis + CloudFront), circuit breakers on downstream calls (Resilience4j), and horizontal auto-scaling up to 100 instances.

**Q: How do services communicate?**
Synchronous REST for real-time needs (LSC calling satellite services). Asynchronous SQS/SNS for non-real-time (analytics events, cache invalidation, inventory updates). We use circuit breakers and timeouts on all synchronous calls to prevent cascading failures.

**Q: How do you handle a region going down?**
All critical services run in both us-east-1 and us-west-2. Route 53 health checks detect if a region is unhealthy and automatically stops routing traffic there. DNS failover happens within about 60 seconds.

**Q: Why Solr instead of Elasticsearch?**
Solr was chosen before I joined — it was already deeply integrated. At our scale it works well. The trade-off is Solr has a smaller ecosystem than Elasticsearch, but it handles our search workload (complex faceted queries across millions of vehicle listings) very efficiently.
