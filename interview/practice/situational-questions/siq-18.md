**18.** Have you ever had to break a monolith into microservices? Walk me through the process.

========== THIS SECTION IS THE ANSWER ==========

Yes, I was directly involved in decoupling our Spring Framework monolith into microservices at the automotive marketplace.

We followed the Strangler Fig pattern. Instead of a big-bang rewrite, we extracted one domain at a time while the monolith continued serving traffic. The process looked like this:

Step 1: Identify domain boundaries. We mapped the monolith's modules — search, pricing, dealer management, user preferences — and chose pricing as the first extraction because it had the clearest boundary and fewest shared database tables.

Step 2: Create the new service. We built a standalone Spring Boot service with its own PostgreSQL database and REST API that replicated the pricing logic from the monolith.

Step 3: Dual-write and compare. The monolith still handled live traffic, but we routed shadow requests to the new pricing service and compared responses. This caught edge cases we missed.

Step 4: Switch traffic. Once response parity was above 99.5%, we updated the aggregation layer to call the new service instead of the monolith's pricing module. We kept the old code path behind a feature flag for quick rollback.

Step 5: Remove dead code. After running in production for 2-3 weeks with no issues, we deleted the pricing module from the monolith.

We repeated this for each domain over several quarters. The hardest part was shared database tables — some tables were used by multiple modules, so we had to carefully decide ownership and create data sync mechanisms.

**Lesson:** Don't try to extract everything at once. Pick the domain with the clearest boundary first, prove the pattern works, then iterate. The monolith doesn't have to fully disappear — sometimes a smaller monolith for tightly coupled domains is perfectly fine.
