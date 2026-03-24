# Interview Answers: Describe a Key Feature You Built

---

## Feature 1: Monolith to Microservices Migration

### "Tell me about the monolith decomposition you did."

We had a Spring Boot monolith handling search, listings, dealers, analytics, and SEO — all in one deployable artifact serving 30 million users per month. As the team grew to 200+ developers, it became a bottleneck: one team's change could break another's feature, we couldn't scale search independently from analytics, and a memory leak in analytics could take down the entire search functionality.

We used the Strangler Fig pattern — extracted services one at a time rather than a big-bang rewrite. We started with Analytics (least coupling), built it as a new microservice, routed traffic to it via a gateway, validated it, then removed the old code from the monolith. Repeated for each domain.

The hardest part was database decomposition. The monolith shared one PostgreSQL with tightly coupled tables. We identified data ownership per domain, gave each service its own database, and used SQS/SNS for event-driven sync where services needed each other's data. For real-time needs like search calling the listings service, we used REST with circuit breakers via Resilience4j. For non-real-time needs like analytics events, we used async messaging.

The result: independent deployments, horizontal scaling per service, and fault isolation — analytics issues no longer killed search.

---

## Feature 2: SEO and AI Search Optimization

### "Tell me about the SEO work you did."

Three major pieces: Web Vitals optimization, automated metadata management, and AI Search Optimization.

For Web Vitals, we focused on INP (Interaction to Next Paint) and CLS (Cumulative Layout Shift) — the metrics Google uses for ranking. This required optimization on both frontend (lazy loading, image handling, reducing React re-renders) and backend (faster API response times so pages render quicker). It was a cross-stack effort.

For metadata, we had a problem: millions of pages across four brands, and developers were manually managing titles, descriptions, and canonical URLs. We integrated a third-party SEO service where the SEO team manages metadata rules themselves. A cron job ingests that metadata into ElastiCache, and the frontend reads from cache at render time. This turned metadata from a developer bottleneck into self-service for the SEO team.

For sitemap generation, the challenge was scale — potentially billions of URLs. We built cron jobs with filtering rules to prioritize high-value pages and exclude duplicates, staying within search engine guidelines.

The newest initiative was AI Search Optimization — with Google AI Overviews and ChatGPT search emerging, we started migrating Next.js from Pages Router to App Router for React Server Components and streaming SSR, which AI crawlers process more effectively. Our team led that migration.

---

## Feature 3: Natural Language Search with AWS Bedrock

### "Tell me about the natural language search feature."

Traditional search requires users to set filters manually — make, model, price, year. We built a feature where users can type "red SUV under 30k with low mileage near Atlanta" and get relevant results.

It's a serverless service — AWS Lambda calling AWS Bedrock. The architecture has three steps: first, a synonym mapping layer that translates common terms to our actual filter values (e.g., "reliable" maps to Toyota/Honda/Lexus, "family car" maps to SUV/Minivan/Sedan). Second, we send a structured prompt to Bedrock with the user's query, our available filter options, and the synonym context — the model extracts structured filter parameters as JSON. Third, the Lambda parses the JSON, validates the filters, and calls our LSC search API with those parameters.

We made it serverless because most users still use traditional filters — Lambda's pay-per-invocation model means we only pay when the feature is used. It also keeps the AI dependency completely isolated from the core search path — if Bedrock is slow or down, traditional search is unaffected.

We deployed it behind a feature flag, starting with 5% of users, monitoring search conversion rates before full rollout. The key lesson: never put an LLM in the critical path of a latency-sensitive request. Use it to enhance, not block.

---

## Feature 4: Analytics Engine

### "Tell me about the analytics engine you built."

Every frontend app needed to collect user behavior — clicks, page views, scrolls, filter changes — and send it to our analytics platform. Instead of scattering analytics calls throughout the codebase, we built a centralized Analytics Engine using an event bus pattern.

The architecture has three layers: event handlers capture specific interactions (click handler, page view handler, scroll handler, etc.) and publish structured payloads to an in-app event bus. The Analytics Engine subscribes to all events, enriches them with common context (session ID, user ID, device info, timestamp), batches them to reduce network requests, and dispatches them to our analytics platform (pixall), which feeds into Snowflake for data warehousing.

The key benefit is decoupling — application components don't know anything about analytics. They just fire events. Adding a new event type is one new handler, zero changes to the Analytics Engine. The same engine and event bus are shared across all frontend microsites — Next.js apps, WordPress, Sitecore — ensuring consistent data collection across all brands.

This pattern also makes testing straightforward. Each layer is independently testable — you can verify handlers capture the right data, the bus routes correctly, and the engine enriches and batches properly.

---

## Feature 5: Accessible UI Library

### "Tell me about the accessibility work."

The legacy UI library wasn't built with accessibility in mind. To comply with US ADA and WCAG requirements, we initially used AudioEye — a third-party overlay that injects scripts to patch accessibility gaps at runtime. Over time, it became unmaintainable: hundreds of scripts, many outdated or conflicting with new features, and constant cleanup burden. It was a band-aid, not a real fix.

We decided to build a new UI library from scratch with accessibility baked in from day one — proper ARIA attributes, keyboard navigation, screen reader support, focus management, color contrast ratios. The library uses unified design tokens shared across all four brands, with each brand applying its own theme on top. Components are API-compatible with the legacy library where possible for easier migration.

The migration is incremental — both libraries coexist, and teams migrate page by page, prioritizing high-traffic pages first. My team contributed components to the new library and was an early adopter, integrating them into our search pages (find-cars, find-dealers).

The trade-off: building accessible components takes 30-40% more effort upfront than non-accessible ones. But the ROI is clear — we eliminated AudioEye's licensing cost, removed hundreds of brittle overlay scripts, and built components that are inherently compliant rather than patched after the fact.

---

## Feature 6: LSC API Development

### "Tell me about API development on the search service."

The LSC (Listing Search Composition) is the central aggregation service — it's the single API that all frontends and many internal services call for vehicle data. Any new feature the frontend needs — a new filter, a new sorting option, a new data field on the vehicle card — requires a new or modified API on the LSC.

My responsibilities: design the endpoint and request/response contract using OpenAPI specs (ensuring backward compatibility since many consumers depend on LSC), implement it in Spring Boot orchestrating calls to Solr and satellite services, and ensure it meets latency requirements for 30M monthly visits. That meant aggressive caching with ElastiCache, optimizing Solr queries, and minimizing downstream service calls.

The key challenge: LSC serves not just our frontend but also other teams across the department. So every API must be designed for broader reusability — not just "what does my page need right now" but "what would any consumer of vehicle search data need." This required coordination across teams and careful API versioning to avoid breaking existing consumers when adding new features.
