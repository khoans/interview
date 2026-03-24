**70.** How did you encrypt communication between your microservices?

========== THIS SECTION IS THE ANSWER ==========

TLS for all inter-service communication within the VPC, plus service-to-service authentication using internal JWTs — no plaintext traffic, even internally.

On the automotive marketplace, all microservices ran in ECS within a private VPC (no public internet exposure for internal APIs). Inter-service HTTP calls used TLS 1.2+ — we terminated TLS at the internal ALB (Application Load Balancer) using AWS Certificate Manager certificates. This meant traffic between services was encrypted in transit, even within the same AWS region. For service-to-service authentication (ensuring only our services could call each other, not a compromised container), we used internal service JWTs — each service had a client credential that it used to obtain a short-lived token for calling other services. This was managed through Spring Security's OAuth2 client credentials flow. Sensitive data like database connections used TLS as well — RDS PostgreSQL enforced SSL connections via the require_ssl parameter, and DynamoDB connections go through AWS SDK which uses HTTPS by default.

The trade-off: TLS everywhere adds latency (~1-2ms per hop for TLS handshake, amortized with connection pooling) and certificate management overhead. We used AWS Certificate Manager for automatic renewal, so certificate expiry was never a manual burden. The lesson: "it's inside the VPC so it's safe" is a dangerous assumption. If an attacker breaches one container, unencrypted internal traffic gives them everything. Defense in depth means encrypting even internal communication.
