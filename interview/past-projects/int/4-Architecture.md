# Architecture

## High-Level Architecture Diagram

```
  Korean Market Site (*.kr)
         │
         ▼
  ┌──────────────────┐
  │   Cloudflare /   │
  │   DNS Layer      │
  └────────┬─────────┘
           │
           ▼
  ┌──────────────────────────────────────────────────┐
  │               EC2 Instance (Main)                │
  │                                                  │
  │  ┌────────────────┐    ┌──────────────────────┐  │
  │  │  Next.js       │    │  Spring Boot         │  │
  │  │  (Frontend)    │    │  (Backend API)       │  │
  │  │                │    │                      │  │
  │  │  - Storefront  │◄──►  - REST APIs         │  │
  │  │  - Admin Panel │    │  - Business Logic    │  │
  │  │  - SSR for SEO │    │  - Auth & Security   │  │
  │  └────────────────┘    └──────────┬───────────┘  │
  │                                   │              │
  └───────────────────────────────────┼──────────────┘
                                      │
                          ┌───────────┼───────────┐
                          │           │           │
                          ▼           ▼           ▼
                   ┌──────────┐ ┌──────────┐ ┌──────────────┐
                   │ MariaDB  │ │ S3       │ │ AI Rendering │
                   │          │ │ (Images/ │ │ Engine       │
                   │ Products │ │  Assets) │ │ (FlaskAPI)   │
                   │ Orders   │ │          │ │              │
                   │ Users    │ │          │ │ Google Nano  │
                   │ etc.     │ │          │ │ Banana       │
                   └──────────┘ └──────────┘ │              │
                                             │ Separate EC2 │
                                             └──────────────┘
```

## Frontend Layer

The frontend is built with **Next.js** and serves two main interfaces:

### Customer-Facing Storefront
- Product browsing, search, and filtering
- Product detail pages with AI visualization integration
- Shopping cart and checkout
- User account and order history
- Server-side rendering (SSR) for SEO optimization in the Korean market

### Admin Panel
- Product and inventory management
- Order processing and tracking
- User and role management
- Content management for banners and promotions
- Analytics and reporting dashboard

Both interfaces are part of the same Next.js application, with route-based separation between the storefront (`/`) and admin panel (`/admin`).

## Backend Layer

The backend is a **Spring Boot** application providing REST APIs for both the storefront and admin panel:

- **Product APIs** — CRUD for products, categories, variants, pricing
- **Order APIs** — order creation, status updates, payment processing
- **User APIs** — registration, authentication, profile management
- **AI Integration API** — proxy to the AI rendering engine, handling image upload and result retrieval
- **Admin APIs** — management endpoints with role-based access control

## Database Layer

**MariaDB** serves as the primary database:

- Products, categories, and inventory
- Orders and order items
- User accounts and authentication
- Consulting service bookings
- Application configuration and settings

## AI Rendering Engine (Separate Service)

The AI rendering engine runs on a **dedicated EC2 instance** to isolate its compute-heavy workload from the main application:

- Built with **FlaskAPI** (Python)
- Uses **Google Nano Banana** for AI image generation
- Exposes REST APIs consumed by the Spring Boot backend
- Processes room photos + product data → generates visualization images
- Separate scaling and deployment lifecycle from the main application

## Storage

- **S3** — stores product images, user-uploaded room photos, and AI-generated visualization images
- **MariaDB** — stores all relational data (products, orders, users)

## Key Architecture Decisions

| Decision | Reasoning |
|----------|-----------|
| **Single EC2 for main app** | Low concurrent user count doesn't justify multi-instance setup or container orchestration |
| **Separate EC2 for AI engine** | AI rendering is compute-heavy; isolating it prevents it from impacting the main application's performance |
| **MariaDB over PostgreSQL** | Client preference and existing team expertise; MariaDB meets all requirements for this scale |
| **Next.js with SSR** | Korean market requires good SEO; SSR provides better search engine indexing |
| **Blue-green deployment** | Minimizes downtime during deployments on a single EC2 instance |
