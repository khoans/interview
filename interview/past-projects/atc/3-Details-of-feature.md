# Details of Features

---

## 1. Decoupling the Spring Framework Monolith into Microservices

### 1.1 Context

The existing system was a Spring Framework monolith that handled multiple domains — vehicle search, listing details, dealer information, user analytics, and SEO metadata — all within a single deployable artifact. As traffic grew to 30 million visits per month and the team scaled, the monolith became a bottleneck for both development velocity and operational stability.

### 1.2 Why We Migrated

- **Deployment risk:** A single change in one domain required redeploying the entire application, increasing the blast radius of every release.
- **Scaling limitations:** The search-heavy workload had very different scaling needs than the dealer management or analytics modules, but we could only scale the entire monolith uniformly.
- **Team autonomy:** Multiple teams were working on the same codebase, causing frequent merge conflicts and long release cycles.
- **Fault isolation:** A memory leak or failure in one module (e.g., analytics) could bring down the entire system, including the critical search functionality.

### 1.3 How We Approached It

#### Step 1: Identify Domain Boundaries

We analyzed the monolith to identify bounded contexts using Domain-Driven Design (DDD) principles. We mapped out the major domains:

- **Search Aggregation Service** — core search logic, Solr interaction, filtering, sorting
- **Listing Service** — vehicle listing details, images, pricing
- **Dealer Service** — dealer profiles, inventory management
- **Analytics Service** — user behavior tracking, event collection
- **SEO Service** — metadata generation, sitemap management

We looked at which modules had the least coupling to others — those became the first candidates for extraction.

#### Step 2: Strangler Fig Pattern

Rather than a big-bang rewrite, we applied the **Strangler Fig Pattern**:

1. Identify a module to extract (e.g., Analytics Service).
2. Build the new microservice alongside the monolith.
3. Route traffic to the new service using a proxy/gateway layer.
4. Once validated, remove the old code from the monolith.
5. Repeat for the next module.

This allowed us to migrate incrementally with zero downtime and the ability to roll back at any point.

#### Step 3: Database Decomposition

This was one of the hardest parts. The monolith shared a single PostgreSQL database with tightly coupled tables.

- **Shared tables:** Some tables were accessed by multiple domains (e.g., a `vehicles` table used by both search and listing). We identified ownership — which domain is the source of truth for that data.
- **Data duplication with eventual consistency:** Each microservice got its own database. Where one service needed data owned by another, we used event-driven synchronization via AWS SQS/SNS to replicate the data asynchronously.
- **Database-per-service:** Each new microservice had its own database (some used PostgreSQL, others DynamoDB depending on access patterns). This eliminated cross-service joins at the database level.

#### Step 4: Handling Dependencies Between Services

- **Synchronous communication (REST):** For real-time needs (e.g., Search Service calling Listing Service to enrich results), we used REST APIs with Spring Boot. We applied the **Circuit Breaker pattern** (using Resilience4j) to prevent cascading failures when a downstream service was slow or unavailable.
- **Asynchronous communication (Event-Driven):** For non-real-time needs (e.g., analytics events, SEO metadata updates), we used AWS SQS and SNS. Producers published events, and consumers processed them independently. This decoupled services in time and reduced direct dependencies.
- **API Gateway:** We introduced an API Gateway to route client requests to the appropriate microservice, handle authentication, rate limiting, and request aggregation.

#### Step 5: Shared Libraries and Common Concerns

- We created shared libraries for cross-cutting concerns: logging, authentication tokens, error handling, and request tracing.
- We used **Spring Cloud** for service discovery and centralized configuration.
- We standardized API contracts using **OpenAPI** specifications so teams could develop against interfaces rather than implementations.

### 1.4 Challenges and How We Dealt With Them

| Challenge | How We Handled It |
|---|---|
| **Distributed transactions** | Avoided distributed transactions where possible. Used the **Saga pattern** with compensating actions for workflows that spanned multiple services (e.g., a listing update that triggers search index refresh and SEO metadata update). |
| **Data consistency** | Accepted eventual consistency for non-critical paths. For critical paths (e.g., search results), the Search Aggregation Service owned the data and served it directly from Solr. |
| **Increased operational complexity** | Adopted centralized logging (Kibana), distributed tracing, and health check endpoints. Used New Relic for monitoring and alerting. |
| **Network latency** | Minimized synchronous call chains. Used caching (Elasticache/Redis) aggressively for frequently accessed data. Kept services within the same AWS region and VPC. |
| **Testing** | Introduced contract testing between services to catch breaking API changes early. Maintained integration test environments that mirrored production topology. |

### 1.5 Results

- Independent deployment cycles — teams could release without coordinating with every other team.
- The Search Service could now scale horizontally based on traffic spikes independently of other services.
- Fault isolation improved — an issue in the Analytics Service no longer impacted search availability.
- Deployment frequency increased significantly as each service had its own CI/CD pipeline via GitHub Actions and AWS CodeDeploy.

---

## 2. Search Engine Optimization (SEO) and AI Search Optimization (AIO)

### 2.1 Web Vitals Optimization

A significant part of SEO work was improving **Core Web Vitals** scores across the customer-facing sites. The key metrics we focused on:

- **INP (Interaction to Next Paint)** — measuring responsiveness when users interact with the page (e.g., clicking filters, sorting results)
- **CLS (Cumulative Layout Shift)** — preventing unexpected layout shifts that hurt user experience and search rankings

This required optimization on both the frontend (React rendering, component lazy loading, image handling) and the backend (reducing API response times so the page renders faster).

### 2.2 Metadata Management — From Manual to Automated

Previously, page metadata (titles, descriptions, canonical URLs, structured data) was **managed manually by developers**. Given the scale of the platform — millions of pages across four brands — this consumed significant developer time and was error-prone.

We integrated with a **third-party SEO service** that allows the SEO team to manage metadata dynamically at scale without requiring developer involvement for every change. The flow:

1. The SEO team configures metadata rules in the third-party platform
2. A **cron job** ingests all metadata from the third-party service and stores it in **ElastiCache** for fast retrieval
3. The frontend applications fetch metadata from ElastiCache at render time, ensuring low-latency access without hitting the third-party API on every request

This shifted metadata management from a developer bottleneck to a self-service capability for the SEO team.

### 2.3 Sitemap Generation

We developed **cron jobs to generate sitemaps** for all four brands. The challenge here was scale — if we were to include every possible URL, there would be **billions of URLs**, which search engines cannot efficiently crawl or process.

To handle this, we applied **filtering rules** to limit which URLs are included in the sitemaps:

- Prioritizing high-value pages (popular search combinations, high-traffic listings)
- Excluding low-value or duplicate pages
- Following search engine guidelines on sitemap size limits

These cron jobs run on a schedule to keep the sitemaps up to date as inventory and pages change.

### 2.4 AI Search Optimization (AIO) — Next.js App Router Migration

Before I left the project, a new initiative emerged: **AI Search Optimization (AIO)**. With the rise of AI-powered search engines (e.g., Google AI Overviews, ChatGPT search), optimizing content for AI consumption became a priority.

This required migrating the Next.js applications from the **Pages Router to the App Router**. The App Router provides:

- **React Server Components** — enabling better streaming and partial rendering, which AI crawlers can process more effectively
- **Improved metadata API** — more granular control over how pages present themselves to AI search engines
- **Streaming SSR** — allowing content to be served progressively, improving both traditional SEO and AI discoverability

Our team was the **main team responsible** for driving this migration across the applications.

---

## 3. Natural Language Search using AWS Bedrock

### 3.1 Context

Traditional vehicle search requires users to manually set filters — make, model, year range, price range, body style, etc. Natural Language Search allows users to type queries like *"red SUV under $30,000 with low mileage near Atlanta"* and get relevant results without interacting with individual filter controls.

### 3.2 Architecture

This is a **serverless service** built on **AWS Lambda + AWS Bedrock**. Its role is to act as a translation layer — converting a user's natural language query into the appropriate LSC API call with the correct filter parameters.

```
User types: "reliable family car under 25k with good gas mileage"
                │
                ▼
    ┌───────────────────────┐
    │   AWS Lambda          │
    │   (NLP Search Service)│
    │                       │
    │  1. Synonym mapping   │
    │  2. Prompt + Bedrock  │
    │  3. Parse AI response │
    │  4. Build LSC request │
    └───────────┬───────────┘
                │
                ▼
    ┌───────────────────────┐
    │   LSC API             │
    │   /search?bodyStyle=  │
    │   SUV,Minivan&price   │
    │   Max=25000&mpgMin=28 │
    └───────────────────────┘
                │
                ▼
        Search results returned to user
```

### 3.3 How It Works

#### Step 1: Synonym Mapping

Before calling the AI model, the service maintains a **synonym dictionary** that maps common natural language terms to the actual filter values the LSC API expects. For example:

| User might say | Maps to filter | Filter value |
|---|---|---|
| "SUV", "sport utility" | bodyStyle | SUV |
| "truck", "pickup" | bodyStyle | Truck |
| "cheap", "affordable", "budget" | priceMax | (context-dependent threshold) |
| "good price", "great deal" | priceDeal | Good/Great deal rating |
| "reliable", "dependable" | make | Toyota, Honda, Lexus (high-reliability brands) |
| "family car" | bodyStyle | SUV, Minivan, Sedan |
| "good gas mileage", "fuel efficient" | fuelEconomy | High MPG range |
| "low mileage" | mileageMax | (threshold value) |
| "clean history", "clean vehicle history" | historyReport | Clean title, no accidents |
| "new", "brand new" | condition | New |
| "used", "pre-owned", "second hand" | condition | Used |
| "vehicle", "car", "automobile", "ride" | (generic) | All vehicle types |

This synonym mapping serves two purposes:

- **Reduces ambiguity** before the query reaches the AI model
- **Ensures consistency** — the AI model's output aligns with the exact filter options available in the LSC API

#### Step 2: Prompt Engineering with AWS Bedrock

The service sends a **structured prompt** to the AWS Bedrock LLM that includes:

1. **System instructions** — defining the AI's role as a search query parser
2. **Available filter options** — the complete list of filters the LSC API supports (make, model, bodyStyle, priceMin, priceMax, yearMin, yearMax, mileageMax, fuelType, color, etc.)
3. **Synonym context** — the synonym mappings so the AI can resolve ambiguous terms
4. **The user's query** — the raw natural language input
5. **Expected output format** — a structured JSON response containing the extracted filter key-value pairs

The prompt instructs the model to extract structured filter parameters from the natural language input and return them in a format that can be directly mapped to LSC API query parameters.

#### Step 3: Parse and Call LSC

The Lambda function:

1. Parses the structured JSON response from Bedrock
2. Validates the extracted filters against the known LSC API parameters
3. Constructs the appropriate LSC API endpoint call with the filter parameters
4. Returns the search results back to the frontend for display

### 3.4 Why Serverless

- **Cost-efficient** — natural language search is not used on every request; most users still use traditional filters. Lambda's pay-per-invocation model means we only pay when the feature is actually used.
- **Scalable** — Lambda scales automatically with demand without provisioning dedicated compute.
- **Isolated** — keeps the AI/ML dependency separate from the core search path, so any issues with Bedrock do not affect traditional search.

---

## 4. Development of an Analytics Engine

### 4.1 Context

Every frontend application needs to collect user behavior data — clicks, page visits, scrolls, chat interactions, etc. — and send it to our analytics platform (pixall). Rather than scattering analytics calls throughout the codebase, we built a centralized **Analytics Engine** component using an **event bus pattern** to decouple event collection from event dispatching.

### 4.2 Architecture

```
  ┌─────────────────────────────────────────────────────────┐
  │                   Frontend Application                  │
  │                                                         │
  │  ┌─────────────────┐  ┌──────────────────────────────┐  │
  │  │  User Actions   │  │      Event Handlers          │  │
  │  │                 │  │                              │  │
  │  │  - Click        │──►  clickEventHandler           │  │
  │  │  - Page visit   │──►  pageViewEventHandler        │  │
  │  │  - Scroll       │──►  scrollEventHandler          │  │
  │  │  - Chat assist  │──►  chatAssistanceEventHandler  │  │
  │  │  - Filter       │──►  filterChangeEventHandler    │  │
  │  │    change       │  │          │                    │  │
  │  └─────────────────┘  └──────────┼────────────────────┘  │
  │                                  │                       │
  │                                  ▼                       │
  │                       ┌──────────────────┐               │
  │                       │    Event Bus     │               │
  │                       │  (Pub/Sub)       │               │
  │                       └────────┬─────────┘               │
  │                                │                         │
  │                                ▼                         │
  │                       ┌──────────────────┐               │
  │                       │ Analytics Engine │               │
  │                       │                  │               │
  │                       │ - Subscribes to  │               │
  │                       │   all events     │               │
  │                       │ - Enriches data  │               │
  │                       │ - Batches events │               │
  │                       │ - Sends to pixall│               │
  │                       └────────┬─────────┘               │
  │                                │                         │
  └────────────────────────────────┼─────────────────────────┘
                                   │
                                   ▼
                          ┌─────────────────┐
                          │     pixall      │
                          │ (Analytics      │
                          │  Platform)      │
                          └────────┬────────┘
                                   │
                                   ▼
                          ┌─────────────────┐
                          │   Snowflake     │
                          │ (Data Warehouse)│
                          └─────────────────┘
```

### 4.3 How It Works

#### Event Handlers

Each type of user interaction has a dedicated **event handler** responsible for capturing the relevant data:

- **clickEventHandler** — captures click events (which element, where on the page, what listing was clicked)
- **pageViewEventHandler** — captures page visits (URL, referrer, timestamp, search parameters)
- **scrollEventHandler** — captures scroll depth (how far down the page the user scrolled)
- **chatAssistanceEventHandler** — captures interactions with the chat assistant feature
- **filterChangeEventHandler** — captures when users change search filters (make, model, price range, etc.)

Each handler is attached as an event listener to the relevant DOM elements or application lifecycle hooks. When a user interaction occurs, the handler captures the event data, structures it into a standardized event payload, and **publishes it to the event bus**.

#### Event Bus (Pub/Sub)

The **event bus** acts as a lightweight in-app pub/sub system that decouples event producers (the handlers) from event consumers (the Analytics Engine). This provides several benefits:

- **Decoupling** — components that trigger events don't need to know anything about analytics. They just publish to the bus.
- **Single responsibility** — handlers focus on capturing the right data; the Analytics Engine focuses on delivering it.
- **Extensibility** — adding a new event type is as simple as creating a new handler and publishing to the bus. No changes needed in the Analytics Engine.

#### Analytics Engine

The **Analytics Engine** component subscribes to all events on the event bus and is responsible for:

1. **Enriching events** — adding common context like session ID, user ID, page URL, timestamp, device info
2. **Batching** — grouping multiple events together to reduce the number of network requests to pixall
3. **Sending to pixall** — dispatching the enriched, batched events to the analytics platform

### 4.4 Why This Pattern

| Benefit | Explanation |
|---|---|
| **Separation of concerns** | Application components don't have analytics logic mixed in. They just fire events. |
| **Consistency** | All events go through one pipeline, ensuring consistent enrichment and formatting. |
| **Testability** | Each layer (handlers, event bus, engine) can be tested independently. |
| **Reusability** | The Analytics Engine and event bus are shared across all frontend microsites — every Next.js app, WordPress, and Sitecore all use the same pattern. |

---

## 5. New UI Library with Accessibility

### 5.1 Context — Why a New UI Library

The legacy UI library was built without **accessibility** as a design consideration. To meet US accessibility compliance requirements (ADA, WCAG), we had initially adopted **AudioEye** — a third-party accessibility overlay provider that injects scripts into the site to patch accessibility gaps at runtime.

Over time, this approach became **unmaintainable**:

- AudioEye injected a large number of scripts to fix accessibility issues across the sites
- As the sites evolved, many of these scripts became outdated or conflicted with new features
- **Cleaning up unused AudioEye scripts** became a constant burden — it was difficult to determine which scripts were still needed and which were stale
- The overlay approach was a band-aid, not a real fix — it patched symptoms rather than addressing the root cause of inaccessible components

The decision was made to build a **new UI library from the ground up** with accessibility baked in from day one, eliminating the need for third-party overlays entirely.

### 5.2 Key Design Principles of the New Library

- **Accessibility-first** — every component is built with WCAG compliance from the start: proper ARIA attributes, keyboard navigation, screen reader support, focus management, color contrast ratios, etc.
- **Unified design tokens** — a single set of design tokens (colors, spacing, typography, breakpoints) shared across **all four brands** (Autotrader, KBB, Ford Blue Advantage, and the fourth brand). Each brand applies its own theme on top of the shared tokens, ensuring visual consistency while allowing brand differentiation.
- **Drop-in replacement** — components are designed to be API-compatible where possible with the legacy library, making migration less disruptive for consuming applications.

### 5.3 Migration Strategy

The migration from the old library to the new one is incremental — both libraries coexist in the applications during the transition. Teams migrate components page by page, prioritizing high-traffic and high-visibility pages first.

### 5.4 My Involvement

Our team was actively involved in developing components for the new UI library to help the project keep pace with the **release train**. This included:

- Building accessible components that meet WCAG standards
- Implementing design tokens that work across multiple brand themes
- Coordinating with the core UI library team to ensure our contributions aligned with the library's architecture and conventions
- Integrating the new components into our own frontend applications (find-cars, find-dealers) as early adopters

---

## 6. Developing New APIs for the LSC Service

### 6.1 Context

As part of ongoing feature development on the main search pages, we regularly designed and implemented **new APIs on the LSC (Listing Search Composition) service** to serve UI features. The LSC is the central aggregation layer, so any new data the frontend needs — whether it's a new filter option, a new sorting mechanism, a new card layout with additional vehicle details, or a new promotional placement — requires a corresponding API on the LSC to aggregate and serve that data.

### 6.2 Responsibilities

- **API design** — defining endpoints, request/response contracts using **OpenAPI** specifications, ensuring backward compatibility for existing consumers
- **Implementation** — building the endpoints in **Spring Boot**, orchestrating calls to Solr and the relevant satellite services to gather the required data
- **Performance** — ensuring new APIs meet latency requirements given the high traffic (~30M monthly visits). This involved leveraging caching (ElastiCache), optimizing Solr queries, and minimizing downstream service calls
- **Coordination** — since LSC serves not just our frontend but also other internal consumers across the department, any new API needed to be designed with broader reusability in mind

---

