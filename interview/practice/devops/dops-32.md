**32. Centralized Logging**
Why is centralized logging important? Describe a typical logging stack (e.g., ELK, CloudWatch, Datadog). What should you log and what should you avoid?

========== THIS SECTION IS THE ANSWER ==========

With microservices spread across multiple instances, you can't SSH into each server to read logs. Centralized logging aggregates all logs into one searchable place. Typical stack: application logs → log shipper (Fluentd/Filebeat) → storage and indexing (Elasticsearch/CloudWatch) → visualization (Kibana/Grafana).

In our project, we used Kibana backed by Elasticsearch. All Spring Boot services logged in JSON format (structured logging), shipped via Fluentd to a central Elasticsearch cluster. When a user reported a search issue, we searched by correlation ID across all services in Kibana to trace the request.

What to log: request IDs, user actions, errors with stack traces, performance timings. What NOT to log: passwords, credit card numbers, PII, full request/response bodies (too much volume). We once accidentally logged full user tokens in debug mode — caught it in code review, but it taught us to add a log sanitizer filter that stripped sensitive patterns automatically.
