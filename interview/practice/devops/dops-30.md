**30. Cost Optimization**
What strategies do you use to optimize cloud infrastructure costs? (right-sizing, reserved instances, spot instances, auto-scaling)

========== THIS SECTION IS THE ANSWER ==========

Key strategies: right-size instances based on actual usage (not guessing), use reserved instances for predictable workloads (up to 70% savings), spot instances for fault-tolerant batch jobs, auto-scaling to avoid paying for idle capacity, and cleanup unused resources (orphaned EBS volumes, old snapshots, idle load balancers).

In our project, we used reserved instances for our always-on production ECS cluster and RDS databases — saved about 40% annually. For our nightly data processing pipeline, we used spot instances (70% cheaper) with retry logic since spot instances can be interrupted. Auto-scaling our search service from 4 to 12 instances based on traffic saved us from running 12 instances 24/7.

The trade-off: reserved instances require commitment (1-3 years) — if your architecture changes, you're stuck paying. We got burned once by reserving instances for a service we later migrated to Lambda. Now the rule is: only reserve after a service has been stable for 6+ months.
