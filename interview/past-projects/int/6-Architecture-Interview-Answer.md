# Interview Answer: Describe the Architecture of Your Current Project

## Short Version (~60 seconds)

It's a relatively straightforward architecture for an ecommerce platform serving the Korean market. The standout feature is an AI-powered product visualization engine.

The frontend is a Next.js application with SSR for SEO — it serves both the customer storefront and an admin panel, separated by routes. The backend is a Spring Boot REST API handling products, orders, users, and the integration with the AI engine.

The AI rendering engine is a separate service — FlaskAPI with Google Nano Banana running on its own EC2 instance. When a user uploads a room photo, the Spring Boot backend sends it to the AI engine, which generates a realistic image showing our products in the user's room. We isolated the AI engine on a separate instance because it's compute-heavy and we didn't want it affecting the main app's performance.

For data, MariaDB handles all relational data, S3 stores images — product photos, user uploads, and AI-generated results. Deployment is simple: a single EC2 instance with blue-green deployment via Bitbucket Pipelines.

---

## Longer Version (~2-3 minutes, if interviewer asks for more detail)

**Frontend:** Single Next.js application handling two interfaces. The storefront at `/` has product browsing, search, filtering, cart, checkout, and the AI visualization feature. The admin panel at `/admin` handles product CRUD, order management, user roles, and promotional content. We use SSR because SEO matters for the Korean market — products need to be discoverable on Naver and Google Korea.

**Backend:** Spring Boot REST API. It exposes endpoints for products, orders, users, and critically — the AI integration. When a user triggers the visualization feature, the backend receives the room photo, determines which products to render based on the current page context, and proxies the request to the AI engine. It also handles authentication, role-based access control for admin vs regular users, and payment processing.

**AI Rendering Engine:** This is the most interesting part architecturally. It's a Python FlaskAPI service using Google Nano Banana for AI image generation, running on a dedicated EC2 instance. The backend sends a room photo plus product data, and the AI engine generates a composite image showing the products in the user's room. We're currently optimizing it to generate multiple angles in a single request — previously each angle was a separate API call, which was slow and expensive. Isolating it on its own EC2 was a deliberate decision — AI rendering can spike CPU to 100% for several seconds per request, and we didn't want that competing with the main app's resources.

**Data layer:** MariaDB for all relational data — products, categories, orders, users, bookings for consulting services. S3 for all binary assets — product images, user-uploaded room photos, and the AI-generated visualization images. The backend writes to both.

**Infrastructure:** Simple and cost-effective. The main app runs on a single EC2 instance — the platform doesn't have massive concurrent traffic, so multi-instance or container orchestration would be over-engineering. We use blue-green deployment: deploy the new version alongside the old one, health check it, swap Nginx routing, then stop the old version. CI/CD is Bitbucket Pipelines — builds the JAR, copies it to EC2, runs the deployment script.

---

## Common Follow-Up Questions & Answers

**Q: Why a single EC2 instead of auto-scaling or containers?**
The traffic doesn't justify it. We serve the Korean market with a modest user base. Running ECS or Kubernetes for this would add operational complexity and cost without real benefit. If traffic grows significantly — especially when we launch the international version — we'd move to an auto-scaling group behind a load balancer. But right now, single EC2 with blue-green is the right trade-off: simple, cheap, near-zero downtime.

**Q: Why is the AI engine separate instead of embedded in the Spring Boot app?**
Resource isolation. AI image generation is CPU-intensive — one request can peg the CPU for 3-5 seconds. If it ran inside the Spring Boot app, a few concurrent AI requests would starve the normal product browsing and checkout flows. Separate EC2 means the AI engine can spike to 100% CPU without affecting the main app. It also has a different deployment cadence — the AI engine changes much less frequently than the ecommerce features.

**Q: How does the AI visualization feature work?**
User uploads a room photo on the product page → frontend sends it to the Spring Boot backend → backend identifies the products being viewed and calls the AI engine API with the photo + product data → AI engine composites the products into the room photo → generated image is stored in S3 and the URL is returned to the frontend. We're optimizing to generate multiple viewing angles in one request to reduce latency from ~10 seconds (3 separate calls) to ~5 seconds (1 batched call).

**Q: What about the international version?**
The Korean version uses Spring Boot + Next.js. The international version targeting the US market uses Node.js + Shopify. Different tech stack because Shopify handles inventory, payments, and checkout — so the backend is much thinner. We'll handle that version next, and the main challenge will be integrating the AI visualization feature with Shopify's product data model.

**Q: How would you scale this if traffic grew 10x?**
Three steps: first, put the EC2 behind an ALB with auto-scaling group — horizontal scaling with no code changes. Second, add a Redis cache for frequently accessed product data and AI results — many users view the same products, so caching visualization results by product+room combo would reduce AI engine load dramatically. Third, if AI engine becomes the bottleneck, move it behind a load balancer with multiple instances, or consider moving to a GPU-backed instance type for faster rendering.
