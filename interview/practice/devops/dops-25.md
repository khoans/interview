**25. DNS and Service Discovery**
How does DNS work in a cloud/microservices environment? What is service discovery and why is it needed?

========== THIS SECTION IS THE ANSWER ==========

DNS translates domain names to IP addresses. In microservices, service discovery solves "how does Service A find Service B?" — especially when instances scale up/down and IPs change constantly. Two approaches: client-side discovery (service queries a registry like Eureka) and server-side discovery (load balancer handles it, like AWS Cloud Map).

In our project, ECS services registered themselves with AWS Cloud Map, which provided DNS-based service discovery. Service A called `search-service.internal` and Cloud Map resolved it to healthy container IPs. When containers scaled or restarted, Cloud Map updated DNS records automatically within seconds.

The trade-off: DNS-based discovery is simple but DNS caching can cause stale IPs — a terminated container might still receive traffic until the TTL expires. We set TTL to 10 seconds to minimize this. For latency-critical paths, some teams prefer a service mesh (like Istio) with real-time discovery, but that adds significant operational complexity.
