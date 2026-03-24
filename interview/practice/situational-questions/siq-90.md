**90.** How did you measure and improve your application's Web Vitals / page load time?

========== THIS SECTION IS THE ANSWER ==========

We tracked Core Web Vitals through New Relic Browser monitoring and Lighthouse CI in our GitHub Actions pipeline, specifically for our vehicle search results page — the highest traffic page at 30M monthly views.

Our initial LCP (Largest Contentful Paint) was 4.2 seconds, CLS (Cumulative Layout Shift) was 0.35, and FID (First Input Delay) was 180ms. For LCP, we implemented server-side rendering with Next.js streaming, lazy-loaded below-the-fold vehicle cards with Intersection Observer, and optimized images with next/image (WebP format, responsive sizes). LCP dropped to 1.8 seconds. For CLS, we added explicit width/height to vehicle images and skeleton placeholders for async content — CLS went to 0.05. For FID, we reduced the JavaScript bundle from 380KB to 210KB using dynamic imports and code splitting — FID dropped to 60ms. We also set up Lighthouse CI with a performance budget: any PR that degraded LCP by more than 200ms was flagged automatically.

Trade-off: SSR improves initial page load but increases server CPU cost. Our ECS frontend tasks needed to scale from 2 to 6 instances to handle the server rendering load. The SEO and UX benefits justified the cost — organic traffic increased 15% after the improvements.
