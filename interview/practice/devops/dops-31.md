**31. Observability vs Monitoring**
What is the difference between observability and monitoring? What are the three pillars of observability?

========== THIS SECTION IS THE ANSWER ==========

Monitoring tells you when something is wrong (dashboards, alerts on predefined metrics). Observability lets you understand why it's wrong — you can ask new questions about your system without deploying new code. The three pillars: logs (what happened), metrics (how much/how often), and traces (the journey of a request across services).

In our project, we had monitoring first — New Relic dashboards showing error rates and response times. But when a search query was slow, monitoring only told us "it's slow." After adding distributed tracing (correlating a request across search service → Solr → satellite services), we could see exactly which service and which database call caused the slowdown.

The trade-off: full observability (logs + metrics + traces correlated together) is expensive — both in tooling cost and the engineering effort to instrument everything. We prioritized: metrics first (cheap, catches most issues), then logs (for debugging), then traces (only for critical cross-service flows).
