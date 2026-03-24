**8. Rolling Deployment**
Explain rolling deployments. What are the trade-offs compared to blue-green and canary strategies?

========== THIS SECTION IS THE ANSWER ==========

Rolling deployment updates instances one at a time (or in batches). If you have 10 instances, it takes down 2, deploys the new version, waits for them to be healthy, then moves to the next 2. At any point, both old and new versions are running.

We used rolling deployments on Elastic Beanstalk for most of our services. Configuration was straightforward — set batch size to 25%, health check grace period to 60 seconds. It required no extra infrastructure unlike blue-green.

The trade-off: during deployment, both versions serve traffic simultaneously, so your API needs to be backward-compatible. Rollback is also slower — you have to re-deploy the old version through the same rolling process. We used rolling for low-risk services and blue-green for critical ones like the main search API where instant rollback mattered.
