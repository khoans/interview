**17. Container Orchestration**
Why do we need container orchestration? Compare Docker Swarm and Kubernetes at a high level.

========== THIS SECTION IS THE ANSWER ==========

When you have dozens of containers across multiple hosts, you need something to handle scheduling, scaling, health checks, networking, and restarts automatically. That's orchestration. Docker Swarm is simpler, built into Docker, easy to set up but limited in features. Kubernetes is the industry standard — more powerful, more complex, with a massive ecosystem.

In our project, we used AWS ECS (Elastic Container Service), which is AWS's managed orchestration — simpler than Kubernetes but gives you auto-scaling, service discovery, and load balancer integration out of the box. We didn't need the full power of K8s for our use case.

The trade-off: Kubernetes gives you maximum flexibility and portability across clouds, but the operational overhead is real — you need dedicated expertise to manage it. ECS or Cloud Run are pragmatic choices when your team is small and you're already on AWS/GCP. Don't adopt K8s just because it's popular.
