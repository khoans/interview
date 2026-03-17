## 99. What is Spring Actuator?

**Spring Actuator provides production-ready monitoring and management endpoints for Spring Boot applications.**

Health checks, metrics, info, environment, thread dumps — operational visibility without custom code.

**Key endpoints:**
- `/actuator/health` — is the app healthy (database, disk, external services)
- `/actuator/metrics` — JVM memory, HTTP requests, custom business metrics
- `/actuator/info` — version, build time, git commit
- `/actuator/env` — configuration properties (careful with secrets)

**Why it matters:**
- Observability out of the box — no need to build health check endpoints
- Kubernetes integration — liveness/readiness probes use `/health`
- Debugging production issues — thread dumps, heap info without SSH access

**Trade-off:**
- Security risk: exposing `/env` or `/threaddump` leaks sensitive information
- Performance: some endpoints (heap dump, thread dump) are expensive under load
- Information overload: 50+ metrics by default, hard to find what matters

**Real-world example:**
We deployed 20 microservices with Actuator enabled — all endpoints exposed publicly. Security audit found `/actuator/env` revealed database passwords, API keys. Immediate hotfix: disabled all endpoints except `/health` and `/metrics`. Added basic auth for admin endpoints. Later, we used `/health` for Kubernetes readiness probes — auto-healing caught 15 deadlocks in the first month. Custom metrics tracked order processing time — caught a memory leak before it caused outages.

**Rule of thumb:** Expose only `/health` and `/metrics` publicly. Protect admin endpoints with network rules or auth. Add custom health indicators for critical dependencies (database, cache, message queue).
