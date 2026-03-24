**27. CDN (Content Delivery Network)**
What is a CDN and how does it work? When should you use a CDN and what types of content benefit most?

========== THIS SECTION IS THE ANSWER ==========

A CDN caches content at edge servers worldwide so users get data from a nearby location instead of your origin server. It reduces latency and offloads traffic from your backend. Best for static assets (images, JS, CSS), but also for cacheable API responses.

In our project, we used CloudFront in front of our Next.js application. Vehicle images, JavaScript bundles, and CSS were cached at edge locations. For the US market, this cut page load time from ~3 seconds to under 1 second for returning visitors. We also cached search API responses for popular queries with a 60-second TTL.

The trade-off: caching means users might see stale data. We used cache-busting with content hashes in filenames for static assets (e.g., `main.a1b2c3.js`) and short TTLs for API responses. The biggest issue we hit was cache invalidation after a deploy — we had to issue CloudFront invalidations which took 5-10 minutes to propagate globally.
