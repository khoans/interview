# Release Strategy — ATC (Automotive Marketplace)

---

## What is this project's release cycle like? How do you decide when to release new features or updates to production?

We released almost every day. Each release contained about 5-6 pull requests, sometimes up to 10. With around 200 developers working across the platform, we had 80-100 pending PRs daily, so frequent small releases were essential to keep the pipeline moving.

This small release cycle was a deliberate strategy: with only 5-6 PRs per release, when a bug appeared after deployment, we could quickly narrow down which PR caused it — New Relic tags each release with the included PRs, so we'd go straight into those specific pull requests and troubleshoot the code. Compare that to a big weekly release with 50 PRs — finding the culprit would be like finding a needle in a haystack.

We used blue-green deployments on Elastic Beanstalk across both AWS regions (us-east-1 first, then us-west-2) — the new version is deployed to the inactive environment, validated, and then traffic is switched over. Each service had its own independent deployment pipeline via GitHub Actions, so teams could release without waiting for each other.

For critical features, we always made sure integration tests passed before the PR was approved. For high-risk changes (like the monolith-to-microservice migrations), we also used feature flags — the code shipped but stayed disabled until we were confident, then we gradually enabled it for a percentage of users.

---

## How do you handle hotfixes or urgent bug fixes in production? Do you have a separate process for these cases?

With daily releases, most bug fixes naturally went out in the next day's release. But for P1 issues (site down, revenue impact), we had an expedited process: developer creates a hotfix branch off main, fixes the issue, opens a PR with mandatory code review (at least one senior reviewer), CI runs automatically, and once approved we deploy directly to production outside the normal release schedule.

The key rule: hotfix must be the minimal change needed to fix the issue. No bundling unrelated changes.

Severity classification: P1 (site down, revenue impact) — fix immediately, even at 3 AM. P2 (degraded experience for some users) — fix within 24 hours, typically in the next day's release. P3 and below went into the normal sprint backlog.

---

## Have you encountered any challenges with the release process, such as failed deployments or rollbacks? How did you address these issues?

Yes. One notable case: we deployed a database migration that renamed a column, and the new application code went out fine. But when we needed to rollback the application due to a separate issue, the old code couldn't find the renamed column — the rollback broke the service. We had about 15 minutes of degraded search results.

After that incident, we established a hard rule: every database migration must be backward-compatible with the currently running application version. If you need to rename a column, you do it in 3 releases: (1) add new column, (2) migrate data and switch app to new column, (3) drop old column. We also started running rollback tests in staging — deploy new version, then deliberately rollback to verify the old version still works.

---

## Walk me through the process of troubleshooting a failed deployment.

1. **Detect** — New Relic alerts fire on elevated error rates or latency spikes within minutes of deployment. We also watch the deployment dashboard during releases.
2. **Rollback immediately** — Since we use blue-green deployment, the previous environment is still running. We switch traffic back to the old environment via Elastic Beanstalk's environment swap — takes under a minute. Users are back to normal while we investigate.
3. **Identify the culprit PR** — In New Relic, each release is tagged with the pull requests it contains. Since our releases are small (5-6 PRs), we go into each PR's code changes and correlate with the error patterns New Relic is showing — the error stack trace usually points us to the exact file and method, which maps directly to a specific PR.
4. **Troubleshoot the code** — Open the PR in GitHub, review the diff, reproduce the issue locally or in staging. Find the root cause.
5. **Fix and redeploy** — Either revert the offending PR or create a hotfix. Deploy in the next release (same day) or immediately if P1.
6. **Post-mortem** — Write up what happened, why, and what we'll change. Tracked in Jira.

The small release cycle is critical here — with only 5-6 PRs to investigate instead of 50, we typically identified the root cause within 15-30 minutes.

---

## Walk me through the process of rolling back a deployment.

Blue-green deployment makes rollback near-instant because the old environment is still running:

1. The old environment (Blue) is still live with the previous version — it was kept running after the swap to Green
2. Trigger an environment URL swap on Elastic Beanstalk — traffic switches back from Green to Blue
3. Rollback completes in under a minute — no rebuilding, no redeploying, just a traffic switch
4. Verify in New Relic that error rates drop back to baseline
5. For database rollbacks — we don't rollback migrations. Our backward-compatible migration policy means the old application code works with the new schema. If the migration itself is the problem, we write a new forward migration to fix it.

The key to fast rollback: blue-green keeps the old environment alive, backward-compatible migrations ensure the old code works with the new schema, and immutable Docker images (tagged with commit SHA) mean we always know exactly what was running before.

---

## Walk me through the process of identifying and fixing a critical bug in production.

1. **Alert** — New Relic pages the on-call engineer via PagerDuty. Engineer acknowledges within 5 minutes.
2. **Mitigate first** — Swap back to the old environment (under a minute). If it's a feature-specific issue, disable via feature flag instead. The goal is: stop the bleeding first, investigate later.
3. **Identify the PR** — Check New Relic to see which release introduced the issue, then look at the 5-6 PRs in that release. The error trace in New Relic usually points directly to the relevant code change.
4. **Troubleshoot** — Open the suspected PR in GitHub, review the diff, reproduce in staging. Use New Relic distributed tracing and Kibana logs with correlation IDs for detailed debugging.
5. **Fix** — Either revert the PR or create a hotfix branch with a minimal change, PR with senior review, deploy directly.
6. **Verify** — Watch New Relic for 15-30 minutes after the fix to confirm error rates are back to normal.
7. **Post-mortem** — Within 48 hours, blameless post-mortem with the team. If the bug should have been caught by integration tests, we add the missing test case as an action item.

**Monitoring tools:** New Relic (APM, distributed tracing, error tracking, release-to-PR mapping), Kibana (centralized logs), PagerDuty (alerting and on-call rotation).
