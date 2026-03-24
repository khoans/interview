**38. Chaos Engineering**
What is chaos engineering? How does it improve system reliability? Give examples of chaos experiments.

========== THIS SECTION IS THE ANSWER ==========

Chaos engineering is intentionally injecting failures into your system to verify it handles them gracefully — kill a service, add network latency, fill a disk, exhaust connection pool. The goal is to find weaknesses before they become real outages.

In our project, we ran simple chaos experiments: we'd terminate random ECS tasks during business hours to verify auto-scaling and health checks worked correctly. We also simulated us-east-1 degradation by routing all test traffic to us-west-2 to confirm our failover worked. We discovered our DNS failover took 5 minutes instead of the expected 30 seconds — because the health check interval was too conservative.

The trade-off: chaos experiments in production are risky — you need confidence in your blast radius. We started in staging, then did production experiments during low-traffic hours with the team watching dashboards. Netflix runs chaos in peak traffic because their systems are mature enough. Most teams should start smaller.
