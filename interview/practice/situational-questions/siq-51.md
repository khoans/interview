**51.** How did you secure your application? Walk me through the security layers.

========== THIS SECTION IS THE ANSWER ==========

Security is layered — no single mechanism is enough. I think of it as perimeter, application, and data layers.

On the automotive marketplace, the perimeter layer was AWS WAF and CloudFront filtering malicious traffic before it hit our services. At the application layer, we used Spring Security with OAuth2 for authentication, role-based access control for authorization, and input validation on every endpoint using Bean Validation (javax.validation). All inter-service communication within our ECS cluster went through private subnets — no public endpoints for internal APIs. At the data layer, PostgreSQL and DynamoDB both used encryption at rest (AWS KMS), and all traffic was TLS 1.2+ in transit. Secrets (DB credentials, API keys) were stored in AWS Secrets Manager and injected at runtime, never in code or config files. We also had rate limiting on public APIs to prevent abuse and New Relic alerts for anomalous traffic patterns.

The trade-off: more security layers mean more operational complexity and latency. We kept internal service-to-service calls lighter (mutual TLS but no OAuth) while external-facing APIs had full authentication. The lesson is to match security intensity to the threat surface — internal APIs behind a VPC don't need the same protection as public endpoints.
