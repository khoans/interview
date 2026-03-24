# Details of Features

---

## 1. Ecommerce Platform — Full-Stack Feature Development

### 1.1 Context

The Korean ecommerce platform sells furniture, home decor products, and interior design consulting services. I'm responsible for building and maintaining features across the entire stack: customer storefront (Next.js), admin panel (Next.js), and backend APIs (Spring Boot), backed by MariaDB.

### 1.2 Key Features Implemented

#### Product Catalog & Shopping Flow

- Product listing pages with search, filtering by category/price/material, and sorting
- Product detail pages with images, specifications, pricing, and stock availability
- Shopping cart with quantity management, price calculation, and promo code support
- Checkout flow integrated with Korean payment gateways
- Order history and tracking for customers

#### Interior Design Consulting Services

- Service listing with consultant profiles and availability calendars
- Booking system allowing customers to schedule interior design consultations
- Integration between consulting sessions and product recommendations — consultants can attach product suggestions from our inventory to their consultation notes

#### Admin Panel

- Product CRUD — manage products, categories, variants, pricing, and inventory levels
- Order management — view, process, update status, handle cancellations and refunds
- User management — customer accounts, admin roles with role-based access control
- Content management — promotional banners, homepage sections, marketing campaigns
- Sales dashboard with basic analytics — revenue, order count, top-selling products

### 1.3 Technical Details

- **Frontend:** Next.js with SSR for SEO on product pages (important for Korean search engines like Naver)
- **Backend:** Spring Boot REST APIs with proper request validation, error handling, and pagination
- **Database:** MariaDB with normalized schema for products, orders, users; optimized queries with appropriate indexes on frequently filtered columns (category, price range, status)
- **Image handling:** Product images uploaded to S3, served via CDN-cached URLs for fast loading

---

## 2. AI-Powered Product Visualization

### 2.1 Context

The platform's standout feature. Users upload a photo of their room, and the AI generates a realistic image showing how selected products from our inventory would look in that space. This helps users visualize purchases and directly promotes in-stock products.

### 2.2 How It Works

```
User on product page → clicks "Visualize in my room"
        │
        ▼
┌──────────────────────┐
│   Next.js Frontend   │
│                      │
│  1. User uploads     │
│     room photo       │
│  2. Selects products │
│     (or auto from    │
│      current page)   │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│   Spring Boot API    │
│                      │
│  1. Validates upload │
│     (size, format)   │
│  2. Checks AI credit │
│     balance          │
│  3. Stores photo     │
│     in S3            │
│  4. Calls AI Engine  │
│     with photo +     │
│     product data     │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│   AI Rendering       │
│   Engine (FlaskAPI)  │
│                      │
│  - Google Nano Banana│
│  - Generates image   │
│    compositing       │
│    products into     │
│    user's room       │
└──────────┬───────────┘
           │
           ▼
  Generated image stored in S3
  URL returned to frontend
  User sees products in their room
```

### 2.3 My Responsibilities

I handle the **integration layer** between the ecommerce platform and the AI engine:

- **API communication** — designing and maintaining the Spring Boot endpoints that accept room photos, validate inputs, and proxy requests to the AI engine's FlaskAPI
- **Error handling** — handling timeout scenarios (AI rendering can take 5-10 seconds), retry logic for transient failures, and graceful fallback when the AI engine is unavailable (show a "try again later" message without breaking the product page)
- **Performance optimization** — working with the AI team on optimizing multi-angle generation to batch multiple angle renders in a single request, reducing total latency from ~15 seconds (3 separate calls) to ~6 seconds (1 batched call)
- **S3 integration** — uploading user room photos and storing AI-generated results in S3 with proper lifecycle policies

### 2.4 Challenges

| Challenge | How We Handled It |
|-----------|-------------------|
| **AI engine latency (5-10s per request)** | Made the rendering async — user submits, sees a loading state, results appear when ready. No blocking the main product page. |
| **AI engine downtime** | The main storefront works independently. If the AI engine is down, only the visualization feature is unavailable — users can still browse, search, and purchase normally. This isolation is by design (separate EC2). |
| **Large image uploads** | Validate file size and format on frontend (max 10MB, JPEG/PNG only), compress on the backend before sending to AI engine, store originals and results in S3. |
| **Cost control** | AI rendering uses paid API calls — we implemented a credit system to track and limit usage (see Feature 3). |

---

## 3. AI Credit Management System

### 3.1 Context

Each AI rendering request costs money (compute time on the AI engine). Without controls, users could generate unlimited images, driving up costs. We needed a system to track, limit, and manage AI credit usage.

### 3.2 How It Works

- Each user account has an **AI credit balance** stored in MariaDB
- New users receive a default number of free credits
- Each visualization request **deducts credits** before calling the AI engine
- The backend **checks credit balance before processing** — if insufficient credits, the request is rejected with a clear message to the user
- Admins can **allocate additional credits** via the admin panel (e.g., as part of a promotion or consulting package)
- A **usage history table** tracks every credit deduction with timestamp, user ID, product ID, and result image URL — for auditing and cost analysis

### 3.3 Technical Details

- **Pre-check pattern:** The Spring Boot API checks credits before calling the AI engine — avoids wasting compute on requests that shouldn't be processed
- **Atomic deduction:** Credit deduction uses a database transaction with optimistic locking (`UPDATE credits SET balance = balance - 1 WHERE user_id = ? AND balance > 0`) to prevent race conditions where two concurrent requests could overdraw
- **Admin controls:** Admin panel shows credit usage reports per user, per time period, and total platform cost. Admins can adjust credit allocations and set global limits.

### 3.4 Why This Matters

Without the credit system, we had no visibility into AI rendering costs. After implementing it, the client could see that 80% of AI usage came from 5% of users, and adjusted their pricing model — offering premium users unlimited renders and free-tier users 5 renders per month. This turned the AI feature from a cost center into a revenue differentiator.

---

## 4. Bug Fixing & Production Maintenance

### 4.1 Context

As a full-stack developer, a significant part of my work involves identifying and resolving bugs across the entire stack — frontend rendering issues, backend API errors, and database inconsistencies.

### 4.2 Notable Examples

#### Frontend Rendering Bug — SSR Hydration Mismatch
The product page showed different content on initial load (server-rendered) vs after React hydrated (client-rendered). Root cause: the server and client were using different locale settings for price formatting (KRW currency). Fix: ensured locale configuration was consistent between SSR and client-side by passing it as a prop from the server rather than reading from `navigator.language`.

#### Backend API Error — Concurrent Order Creation
Two users could occasionally purchase the last item in stock simultaneously, resulting in negative inventory. Root cause: the stock check and stock decrement were in separate database calls without a transaction lock. Fix: wrapped the entire order creation flow in a database transaction with `SELECT ... FOR UPDATE` on the inventory row to prevent concurrent reads of the same stock.

#### Database Inconsistency — Orphaned Order Items
Order items existed in the database without a corresponding parent order — caused by a partial failure in the order creation flow where the order insert succeeded but order items insert failed (network timeout to DB). Fix: wrapped order + order items creation in a single transaction so both succeed or both rollback. Added a cleanup job to reconcile and remove existing orphaned records.

### 4.3 Approach

1. **Reproduce** — replicate the bug locally or in staging with specific steps
2. **Trace** — check application logs on EC2, query MariaDB for data state, check S3 for file integrity
3. **Root cause** — identify the actual cause, not just the symptom
4. **Fix minimally** — smallest change that fixes the issue
5. **Verify** — test the fix against the original reproduction steps
6. **Deploy** — push via Bitbucket Pipeline, blue-green deployment
7. **Monitor** — watch Prometheus/Grafana after deployment to confirm the fix holds

---

## 5. Database Management & Optimization

### 5.1 Context

MariaDB is the single database for the entire platform — products, orders, users, consulting bookings, AI credits, and configuration. As features grow, the schema evolves and query performance needs attention.

### 5.2 Key Work

#### Schema Design
- Designed the schema for the AI credit system (credits table, usage history, admin allocations)
- Designed the consulting booking system tables (consultants, availability slots, bookings, consultation notes with product references)
- Applied normalization where appropriate but denormalized for read-heavy pages (e.g., product listing page stores category name directly instead of joining every time)

#### Query Optimization
- Added composite indexes on frequently filtered columns: `(category_id, status, price)` for product listing queries
- Identified and fixed a slow query on the order management page — the admin panel was loading all orders with a JOIN to order items and users. Added pagination, filtered by date range by default, and added an index on `created_at`
- Optimized the "top-selling products" dashboard query from a full table scan (~8 seconds) to an indexed aggregation (~200ms) by adding a materialized summary table updated nightly

#### Migration Management
- All schema changes managed through versioned migration scripts
- Migrations applied during deployment — the blue-green process runs migrations before starting the new version
- Rule: all migrations must be backward-compatible (add columns, never remove or rename in the same release)

---
