**39.** How do you monitor your application in production? What tools do you use?

========== THIS SECTION IS THE ANSWER ==========

We used a combination of New Relic for APM, Kibana for log aggregation, and CloudWatch for infrastructure metrics.

**New Relic APM:** This was our primary tool. It provided real-time visibility into response times, throughput, error rates, and database query performance for every service. The most valuable feature was distributed tracing — I could trace a single search request from the aggregation service through Solr, pricing service, and dealer service, seeing exactly where time was spent. We had dashboards showing p50, p95, and p99 latency for critical endpoints.

**Kibana (ELK stack):** All services logged to a centralized Elasticsearch cluster via Filebeat. We used structured JSON logging with a correlation ID that propagated across services. When debugging an issue, I could search Kibana for a specific correlation ID and see the entire request journey across all services. We also built dashboards for business metrics — search volume by vehicle type, popular filters, conversion events.

**CloudWatch:** Monitored infrastructure — ECS CPU/memory utilization, RDS connection count and IOPS, SQS queue depth, Lambda invocation errors. CloudWatch alarms triggered PagerDuty alerts for critical thresholds.

**Alerting tiers:** We had three levels — P1 (PagerDuty page, >5% error rate or service down), P2 (Slack notification, >1% error rate or latency spike), P3 (daily digest, slow queries or queue backlog). This prevented alert fatigue while ensuring critical issues got immediate attention.

**Lesson:** Monitoring without alerting is just data collection. Alerting without runbooks is just noise. For every alert, we documented: what it means, who to notify, and the first three troubleshooting steps. This cut our mean time to resolution significantly.
