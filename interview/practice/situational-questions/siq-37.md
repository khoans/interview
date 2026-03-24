**37.** How do you do zero-downtime deployments?

========== THIS SECTION IS THE ANSWER ==========

We used ECS rolling deployments with health checks to achieve zero downtime.

**How it worked:** When deploying a new version, ECS launched new task instances with the updated Docker image alongside the existing ones. Each new instance had to pass health checks (HTTP 200 on /actuator/health, including database connectivity check) before the load balancer routed traffic to it. Once the new instance was healthy, ECS drained connections from an old instance (giving it 30 seconds to finish in-flight requests) and terminated it. This process repeated until all instances were replaced.

**Key enablers:**
- **Backward-compatible database migrations:** Flyway ran on startup of the new instance. The schema change had to work with both old and new code simultaneously, since both versions ran during the rollout window.
- **Graceful shutdown:** Spring Boot's server.shutdown=graceful setting ensured in-flight requests completed before the JVM stopped. We set a 30-second timeout matching the ECS deregistration delay.
- **Minimum healthy percent:** We configured ECS to maintain at least 100% of desired count during deployment (minimumHealthyPercent=100, maximumPercent=200), so capacity never dropped below normal.

**Multi-region consideration:** We deployed to us-east-1 first, monitored for 10-15 minutes, then deployed to us-west-2. If the first region showed issues, we stopped the rollout and Route 53 continued routing all traffic to the healthy us-west-2 region running the old version.

**Lesson:** Zero-downtime deployment isn't just about the deployment tool — it requires backward-compatible migrations, graceful shutdown handling, and proper health checks. If any of those are missing, you'll still have blips during deployment.
