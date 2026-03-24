**55.** How did you handle CORS in your project? Did you ever have issues with it?

========== THIS SECTION IS THE ANSWER ==========

We configured CORS at the Spring Security level with an explicit allowlist of origins — never wildcard (*) in production.

On the automotive marketplace, our Next.js frontend was served from a different domain than our API services. We configured CORS in Spring Security's SecurityFilterChain, allowing specific origins (our production domain, staging domain, and localhost for development), specific methods (GET, POST, PUT, DELETE), and specific headers (Authorization, Content-Type). We also set Access-Control-Max-Age to 3600 seconds so browsers cached preflight responses and didn't send OPTIONS requests on every API call. One issue we hit: our CDN (CloudFront) was stripping the Vary: Origin header, causing cached responses with the wrong CORS headers to be served to different origins. We fixed it by adding Origin to CloudFront's cache key.

The trade-off: strict CORS is essential for security but adds complexity in multi-environment setups (dev, staging, prod each with different origins). We parameterized the allowed origins via environment variables in AWS so each environment had its own config. The lesson: always test CORS in an environment that mirrors production — localhost often hides CORS issues because browsers are more lenient.
