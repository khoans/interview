**24. Load Balancing**
Explain the different types of load balancers (L4 vs L7). What algorithms are used for distributing traffic?

========== THIS SECTION IS THE ANSWER ==========

L4 (Transport layer) load balancers route based on IP and TCP port — fast but no visibility into request content. L7 (Application layer) balancers understand HTTP — can route based on URL path, headers, cookies. Common algorithms: round-robin, least connections, IP hash, weighted round-robin.

In our project, we used AWS ALB (Application Load Balancer, L7) to route traffic. Path-based routing sent `/api/search/*` to the search service and `/api/inventory/*` to the inventory service. We used least-connections algorithm because our search requests had variable processing times — round-robin would overload instances handling slow queries.

The trade-off: L7 gives you smarter routing and features like sticky sessions, but adds latency (it must parse HTTP headers). L4 is faster but dumb — it can't do path-based routing or terminate SSL. For microservices behind an API gateway, L7 is almost always the right choice. We only used L4 (NLB) for internal gRPC communication where we needed raw TCP performance.
