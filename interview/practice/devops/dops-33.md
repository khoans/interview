**33. Application Metrics**
What metrics should you collect for a web application? Explain the RED method (Rate, Errors, Duration) and USE method (Utilization, Saturation, Errors).

========== THIS SECTION IS THE ANSWER ==========

RED method is for request-driven services: Rate (requests/sec), Errors (failed requests/sec), Duration (latency distribution). USE method is for infrastructure resources: Utilization (% busy), Saturation (queue depth), Errors (error count). RED tells you how your app is doing; USE tells you how your infrastructure is doing.

In our project, we tracked RED metrics for every API endpoint in New Relic — request rate, error rate, and p50/p95/p99 latencies. For infrastructure, we monitored CPU utilization, memory usage, and ECS task queue depth via CloudWatch. The search API's SLO was p99 latency under 500ms.

The trade-off: collecting too many metrics creates noise and cost. We focused on golden signals (RED) for application health and only dove into USE metrics when RED showed a problem. For example, when p99 latency spiked, we'd check CPU saturation to determine if we needed more instances or if there was a code-level issue.
