**75.** How did you handle high traffic spikes? Did your application ever go down under load?

========== THIS SECTION IS THE ANSWER ==========

Our platform handled 30 million visits per month with regular spikes during promotional events. We relied on auto-scaling, caching, and multi-region deployment to handle load.

We had ECS auto-scaling based on CPU and request count — the search service scaled from 4 to 12 instances during peak. ElastiCache Redis cached frequently searched queries (e.g., "Toyota Camry under $25,000" — top 1,000 searches covered about 40% of traffic). We deployed across us-east-1 and us-west-2 with Route 53 latency-based routing. One time, we did have a partial outage: a holiday sales event drove 3x normal traffic, and the Solr cluster couldn't keep up because we hadn't pre-warmed the new instances. Search latency spiked to 15 seconds, triggering cascading timeouts. We mitigated by serving cached results for popular queries and added Solr instances manually. After that, we implemented pre-scaling before known events and added a stale-cache fallback that serves slightly outdated results when Solr is overwhelmed.

Trade-off: pre-scaling costs money for capacity you might not need, but the cost of downtime on a 30M-user platform far exceeds a few hours of extra EC2 instances.
