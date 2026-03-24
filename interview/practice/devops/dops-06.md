**6. Blue-Green Deployment**
Explain the blue-green deployment strategy. What are its advantages and when would you choose it over other strategies?

========== THIS SECTION IS THE ANSWER ==========

Blue-green runs two identical production environments. "Blue" serves live traffic while "Green" gets the new version. After testing Green, you switch the load balancer to point to Green. If something breaks, you switch back to Blue instantly.

In our project, we used this approach for our search aggregation service on Elastic Beanstalk — we'd deploy to the inactive environment, run smoke tests, then swap URLs. Rollback was just swapping back, taking under a minute.

The trade-off: you need double the infrastructure during deployment, which increases cost. It also doesn't handle database schema changes well — if Green needs a new column, Blue can't use it after rollback. For stateless services it's great, but for anything with DB migrations we combined it with backward-compatible migrations.
