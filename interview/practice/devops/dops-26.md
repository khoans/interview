**26. Cloud Regions and Availability Zones**
What are regions and availability zones? How do you design for high availability across multiple regions?

========== THIS SECTION IS THE ANSWER ==========

A region is a geographic location (e.g., us-east-1 in Virginia). Each region has multiple Availability Zones (AZs) — isolated data centers with independent power and networking. Multi-AZ protects against data center failure; multi-region protects against entire region outages.

In our project, all critical services deployed to both us-east-1 and us-west-2. Route 53 with health checks did DNS failover — if us-east-1 became unhealthy, traffic automatically shifted to us-west-2. PostgreSQL used cross-region read replicas that could be promoted to primary during a region failure.

The trade-off: multi-region is expensive (double infrastructure) and complex (data synchronization, eventual consistency). We only did multi-region for customer-facing services. Internal tools and batch jobs ran in a single region — the cost of replicating everything wasn't justified for non-critical workloads.
