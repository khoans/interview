# Architecture

## High-Level Architecture Diagram

```
  autotrader.com                                          kbb.com
       │                                                      │
       ▼                                                      ▼
  ┌──────────────────┐                                ┌──────────────────┐
  │  Akamai Router   │                                │  Akamai Router   │
  └────────┬─────────┘                                └────────┬─────────┘
           │                                                   │
    ┌──────┼──────────┬──────────────┐              ┌──────────┼──────────┐
    │      │          │              │              │          │          │
    │ /cars-dealer  /research/*   /* │        /cars-for-sale /research/* │  ...
    │      │          │      (homepage)            │          │          │
    ▼      ▼          ▼              ▼              ▼          ▼          ▼
 find-  find-     Sitecore      WordPress       Next.js    Pure       Other
 cars   dealers   CMS           (Homepage)      + bonnet-  Next.js    Next.js
 (Next.js         (Blog)                        next       (no        apps
 + bonnet-next)                                            bonnet)
    │      │          │              │              │          │          │
    └──────┴──────────┴──────┬───────┴──────────────┴──────────┴──────────┘
                             │
                             │    All frontend apps ──► pixall (Analytics Platform)
                             │                              │
                             │                              ▼
                             │                     ┌─────────────────┐
                             │                     │  Data Solution  │
                             │                     │  Department     │
                             │                     │       │         │
                             │                     │       ▼         │
                             │                     │   Snowflake     │
                             │                     │  (Data Warehouse│
                             │                     └─────────────────┘
                             │
                             ▼              All frontend apps consume LSC
  ┌────────────────────────────────────────────────────────────────────────────────┐
  │                           Backend Services                                    │
  │                                                                                │
  │  ┌─────────────┐    ┌───────────────────────────────────────────┐              │
  │  │ Apache Solr │◄───┤                                           │              │
  │  │  (Search    │    │    LSC - Listing Search Composition       │              │
  │  │   Engine)   │    │         (Spring Boot)                     │              │
  │  └─────────────┘    │                                           │              │
  │                     │  - Aggregates data from Solr +            │              │
  │                     │    satellite services                     │              │
  │                     │  - Serves all frontend apps +             │              │
  │                     │    internal consumers                     │              │
  │                     └──────────┬────────────────────────────────┘              │
  │                                │                                               │
  │               ┌────────────────┼────────────────────┐                          │
  │               │  Satellite Services                │                          │
  │               │  ┌────────────┐ ┌───────────────┐  │                          │
  │               │  │ Listings,  │ │Personalization│  │                          │
  │               │  │ Dealers,   │ │   Engine      │  │                          │
  │               │  │ Pricing,   │ │               │  │                          │
  │               │  │ etc.       │ │               │  │                          │
  │               │  └────────────┘ └───────────────┘  │                          │
  │               └────────────────────────────────────┘                          │
  │                                                                                │
  │  ┌─────────────────────┐                                                       │
  │  │  Other Internal     │                                                       │
  │  │  Services/Consumers │──────────────► (also consume LSC)                     │
  │  └─────────────────────┘                                                       │
  │                                                                                │
  │  ┌───────────────────────────────────────────────────────────┐                 │
  │  │             Serverless Services (AWS Lambda)             │                 │
  │  │  ┌──────────────┐  ┌──────────┐  ┌──────────────────┐   │                 │
  │  │  │  NLP Search  │  │ Pricing  │  │  SEO Service     │   │                 │
  │  │  │ (AWS Bedrock)│  │ Service  │  │                  │   │                 │
  │  │  └──────────────┘  └──────────┘  └──────────────────┘   │                 │
  │  └───────────────────────────────────────────────────────────┘                 │
  │                                                                                │
  │  ┌───────────────────────────────────────────────────────────┐                 │
  │  │             Scheduled Jobs (AWS Lambda)                  │                 │
  │  │  ┌───────────┐  ┌───────────┐  ┌───────────────┐        │                 │
  │  │  │ Solr Index│  │  Cache    │  │   Pricing     │        │                 │
  │  │  │ Ingestion │  │  Refresh  │  │ Calculations  │        │                 │
  │  │  └───────────┘  └───────────┘  └───────────────┘        │                 │
  │  └───────────────────────────────────────────────────────────┘                 │
  └────────────────────────────────────────────────────────────────────────────────┘
```

## Frontend Layer — Microsite Architecture

The frontend follows a **microsite architecture** — rather than a single monolithic frontend, we have **multiple independent applications**, each responsible for a specific set of pages. This pattern is applied across multiple brands under the same umbrella.

### Akamai Router (Edge Layer)

**Akamai** sits at the edge and acts as the URL-based router that directs traffic to the correct microsite based on the URL path. From the user's perspective, each brand looks like one seamless site. Under the hood, each route maps to a completely separate application.

**autotrader.com:**

- `/cars-for-sale/*` → **find-cars** (Next.js + bonnet-next) — main vehicle search page
- `/cars-dealer/*` → **find-dealers** (Next.js + bonnet-next) — dealer search pages
- `/research/*` → **Sitecore CMS** — blogging and editorial content
- `/` (homepage) → **WordPress**

**kbb.com:**

- `/cars-for-sale/*` → Next.js + bonnet-next — vehicle search page
- `/research/*` → **pure Next.js** (no bonnet-next framework) — research and editorial pages
- Other pages → mix of pure Next.js and bonnet-next apps depending on the page

### Next.js Applications — Two Flavors

Not all Next.js microsites are built the same way. There are two flavors:

**1. Next.js with bonnet-next (custom Node.js framework)**

Used primarily for search-related pages across both autotrader.com and kbb.com. bonnet-next provides:

- A **middleware pipeline** — each app develops its own middleware plugins that process requests and handle business logic (request transformation, caching headers, authentication, integration with internal services)
- **Third-party integrations** — Akamai, AWS, and other external services are integrated at the framework level
- **Server-side rendering** — leveraging Next.js SSR for SEO support

Each bonnet-next app has its own **custom configuration and plugins** tailored to its bounded context, while sharing the same underlying framework.

**2. Pure Next.js (no bonnet-next)**

Some pages, particularly on kbb.com (e.g., `/research`), are built as **standard Next.js applications** without the bonnet-next wrapper. These are simpler apps that don't need the full middleware pipeline and third-party integration layer that bonnet-next provides.

### WordPress and Sitecore CMS

Beyond the Next.js microsites, the frontend also includes:

- **WordPress** — powers the autotrader.com homepage. It also leverages the LSC to serve vehicle content.
- **Sitecore CMS** — powers the blogging/editorial platform for research and review articles.

### Shared UI Libraries

All frontend applications — regardless of whether they use bonnet-next or pure Next.js — consume from a **shared UI component library**. Currently there are two UI libraries in use:

- **Legacy UI library** — the original component library used across all apps
- **New UI library** — the replacement library being actively developed with improved accessibility support (particularly for users with visual impairments in the US market)

The migration from the old library to the new one is ongoing. Both libraries coexist across the apps during the transition period.

### User Behavior Analytics (pixall)

Every frontend application collects user behavior data — page visits, clicks, scrolls, and other interactions — and sends it to an analytics platform called **pixall**. From there, the **Data Solution department** processes the raw event data and stores it in **Snowflake** (a cloud data warehouse) for reporting, analysis, and feeding back into systems like the Personalization Engine.

### Key Benefits

- Every frontend application — whether Next.js (bonnet-next or pure), WordPress, or Sitecore — consumes the same **LSC (Listing Search Composition)** backend service for vehicle data. This keeps the data layer unified while allowing the presentation layer to be split across different technologies, brands, and teams.
- All apps feed user behavior data into a unified analytics pipeline (pixall → Snowflake), enabling cross-brand insights and personalization.

## Core Backend — Listing Search Composition (LSC)

The central backend service is the **LSC (Listing Search Composition)** service, built with **Spring Boot**. This is an **aggregation service** — it does not own data itself but orchestrates calls to multiple downstream sources:

- **Apache Solr** — the primary search engine for vehicle listings
- **Satellite services** — individual services that own specific domains like listing details, dealer information, pricing, etc.
- **Personalization Engine** — determines which vehicles to surface and in what order based on user behavior and preferences

The LSC is not exclusive to the customer-facing site. It serves as the shared search API for **most internal services across the department**, making it a critical, high-traffic service.

## Serverless Services

Alongside the core services, we have several **AWS Lambda-based serverless services** for specific capabilities:

- **Natural Language Search** — uses AWS Lambda + AWS Bedrock to allow users to search with natural language queries
- **Pricing Service** — handles pricing logic and calculations
- **SEO Service** — manages metadata generation for search engine optimization

## Scheduled Jobs (Cron)

We also develop and maintain a number of **scheduled jobs using AWS Lambda** for background processing:

- **Solr index ingestion** — keeping the search engine data in sync with source systems
- **Cache refresh** — updating cached data to keep it fresh
- **Pricing calculations** — batch pricing updates

## Technology Diversity

The tech stack is **polyglot** across the system:

| Component | Technologies |
|---|---|
| Customer-facing frontend | Next.js, React, custom Node.js framework |
| LSC (core search) | Java, Spring Boot |
| Satellite services | Spring Boot, Express.js, Python (varies by team/domain) |
| Serverless (Lambda) | Python, Node.js, or Java runtime (varies by use case) |
| Search engine | Apache Solr |
| Databases | PostgreSQL, DynamoDB |
| Message queues | AWS SQS, AWS SNS |
