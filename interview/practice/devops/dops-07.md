**7. Canary Deployment**
What is a canary deployment? How does it differ from blue-green? What metrics do you monitor during a canary release?

========== THIS SECTION IS THE ANSWER ==========

Canary deployment routes a small percentage of traffic (say 5%) to the new version while 95% stays on the old one. You gradually increase traffic as confidence grows. Unlike blue-green which is all-or-nothing switch, canary gives you a gradual rollout with real user traffic.

In our project, when deploying changes to the search results page, we'd route 5% of traffic to the new version and monitor error rates, response times (p50, p95, p99), and search conversion metrics in New Relic. If metrics looked good after 15 minutes, we'd bump to 25%, then 50%, then 100%.

The trade-off: canary is safer for high-traffic services but adds complexity — you need traffic splitting at the load balancer level and good observability. You also need to handle running two versions simultaneously, which can be tricky with shared caches or stateful sessions.
