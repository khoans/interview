**19. Health Checks in Docker**
How do you implement health checks in Docker? What is the difference between HEALTHCHECK in Dockerfile and orchestrator-level health checks?

========== THIS SECTION IS THE ANSWER ==========

Dockerfile HEALTHCHECK runs a command inside the container to verify the app is healthy (e.g., `curl localhost:8080/actuator/health`). Orchestrator-level health checks (ECS, K8s) probe from outside the container — liveness checks restart unhealthy containers, readiness checks stop routing traffic until the container is ready.

In our Spring Boot services, we used Spring Actuator's `/health` endpoint. The ECS task definition had a health check hitting that endpoint every 30 seconds with a 5-second timeout. If 3 consecutive checks failed, ECS killed the container and started a new one. We also configured a startup grace period of 60 seconds so the container had time to initialize.

The trade-off: Dockerfile HEALTHCHECK only marks the container status — it doesn't restart anything by itself. Orchestrator checks actually take action (restart, stop traffic). We used both: Dockerfile HEALTHCHECK for local development visibility, orchestrator checks for production auto-healing.
