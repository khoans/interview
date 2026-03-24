**15. Docker Compose**
When would you use Docker Compose? How does it differ from Kubernetes? What are its limitations in production?

========== THIS SECTION IS THE ANSWER ==========

Docker Compose defines multi-container applications in a single YAML file — great for local development and simple deployments. Kubernetes is a full orchestration platform with auto-scaling, self-healing, service discovery, and rolling updates across clusters. Compose is "run these containers together"; Kubernetes is "manage these containers at scale."

In our project, we used Docker Compose for local development — one `docker-compose up` spun up the Spring Boot app, PostgreSQL, Redis, and a mock Solr. In production, we used ECS (AWS's managed container orchestration) instead of Compose because we needed auto-scaling, health checks, and multi-region deployment.

The trade-off: Compose is simple and fast to set up but lacks production essentials — no auto-restart on failure, no horizontal scaling, no built-in load balancing. Kubernetes handles all of that but has a steep learning curve and operational overhead. If your team is small and your scale is modest, ECS or Cloud Run can be a pragmatic middle ground between Compose and full Kubernetes.
