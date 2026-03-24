**40. APM (Application Performance Monitoring)**
What is APM? How do tools like New Relic, Datadog, or Dynatrace help you understand application performance?

========== THIS SECTION IS THE ANSWER ==========

APM instruments your application to track request flow, database queries, external API calls, and error rates — all correlated together. It shows you not just that a request was slow, but exactly which method, query, or external call made it slow. Think of it as X-ray vision into your running application.

In our project, New Relic APM showed us that our search endpoint's p99 jumped from 300ms to 2 seconds. Drilling in, we saw it was a specific Solr query that blew up when users searched with 5+ filters — the query plan changed from indexed to full scan. Without APM, we'd have been guessing; with it, we found the exact query in 5 minutes.

The trade-off: APM agents add overhead (typically 2-5% CPU and memory). We ran the agent on all production services but with sampling — capturing detailed traces for 10% of requests and always for errors. Full instrumentation of every request would have added too much overhead for a service handling thousands of requests per second.
