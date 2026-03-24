# Release Strategy — INT (Korean Ecommerce Platform)

---

## What is this project's release cycle like? How do you decide when to release new features or updates to production?

We release on a monthly cycle. Features are developed throughout the month, go through code review and testing, and are bundled into a monthly release. We use a blue-green deployment strategy on our EC2 instance — the new version starts alongside the old one, passes health checks, then Nginx swaps traffic over. This gives us near-zero downtime.

The decision to release is driven by the client's sprint cycle. We work in monthly sprints with the Korean client, demo completed features at the end of each sprint, and release approved features to production. For the AI rendering engine, releases are less frequent — roughly every 6-8 weeks — since that code changes less often.

---

## How do you handle hotfixes or urgent bug fixes in production? Do you have a separate process for these cases?

Yes, hotfixes go out immediately without waiting for the monthly release. The process: developer pushes a fix to a hotfix branch, gets a quick code review, Bitbucket Pipeline builds the JAR and deploys it to EC2 using the same blue-green process. Since it's a single EC2 instance, the deployment is fast — under 5 minutes from merge to live.

For the AI rendering engine, hotfixes follow a similar process but on its separate EC2 instance. We can patch either service independently without affecting the other.

---

## Have you encountered any challenges with the release process, such as failed deployments or rollbacks? How did you address these issues?

Yes. Early on, we had a deployment where the new JAR failed to start because of a missing environment variable that was configured on the developer's machine but not on the EC2 instance. The blue-green deployment script caught it — the new version failed the health check, so the script aborted and the old version kept serving traffic. No downtime, but it took us 30 minutes to realize the environment variable was missing.

After that, we added a pre-deployment checklist that validates all required environment variables are present before starting the new version. We also created a `.env.example` file in the repo documenting every required variable so nothing gets missed.

---

## Walk me through the process of troubleshooting a failed deployment.

1. **Detect** — The blue-green deployment script reports a health check failure. Or Prometheus/Grafana alerts on elevated error rates after a successful swap.
2. **Check logs** — SSH into the EC2 instance and check the Spring Boot application logs. The error is usually in the startup logs (missing config, port conflict, dependency issue) or in the runtime logs (database connection failure, external service timeout).
3. **Identify the cause** — Compare the diff between the old and new JAR. Since we deploy monthly, the diff can be large — but git log helps narrow down which changes are most likely to cause the issue.
4. **Rollback or fix** — If the old version is still running (health check failed before swap), no rollback needed — it never went live. If the swap happened and then we find issues, we re-run the deployment script pointing to the previous JAR file which is kept on the EC2 instance.
5. **Fix and redeploy** — Fix the issue, push through Bitbucket Pipeline, redeploy.

---

## Walk me through the process of rolling back a deployment.

Our blue-green setup makes rollback straightforward:

1. The deployment script keeps the previous JAR file on the EC2 instance (named with a timestamp)
2. To rollback: run the deployment script pointing to the old JAR — it starts the old version on the alternate port, health checks it, and swaps Nginx back
3. Total rollback time: under 2 minutes
4. For database rollbacks — we follow the same rule as any project: migrations must be forward-only and backward-compatible. We've never needed to rollback a migration, but if we did, we'd write a new migration to undo the changes rather than trying to reverse the original.

The simplicity of a single EC2 instance is actually an advantage here — rollback is just "start the old JAR, swap Nginx." No coordinating across multiple instances or regions.

---

## Walk me through the process of identifying and fixing a critical bug in production.

1. **Alert** — Prometheus triggers an alert in Grafana when error rates exceed the threshold or the application goes down. We also get notified via Slack integration.
2. **Assess** — Check Grafana dashboards: is the application up? Is MariaDB connected? Is the AI engine responding? Identify which component is affected.
3. **Investigate** — SSH into the EC2 instance, check Spring Boot logs. For AI engine issues, check the FlaskAPI logs on the separate EC2. Look for stack traces, connection errors, or resource exhaustion.
4. **Mitigate** — If the application is down, restart it (`restart.sh`). If the AI engine is the problem, the main storefront still works — users just can't use the visualization feature. This isolation is by design.
5. **Fix** — Create a hotfix, push through Bitbucket Pipeline, deploy via blue-green.
6. **Verify** — Watch Grafana for 15-30 minutes to confirm stability.
7. **Communicate** — Update the client in the next standup or immediately via Slack if it's a customer-impacting issue.

**Monitoring tools:** Prometheus (metrics collection), Grafana (dashboards and alerting), application logs on EC2 (rotated daily), Slack (notifications).
