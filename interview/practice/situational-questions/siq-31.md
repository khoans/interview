**31.** Walk me through how you deploy code from your local machine to production.

========== THIS SECTION IS THE ANSWER ==========

The flow is: local branch, pull request, automated pipeline, staged environments, production.

1. **Local development.** I work on a feature branch, write code and tests, run the build locally with `mvn verify` to ensure compilation and unit tests pass.

2. **Pull request.** I push the branch and open a PR on GitHub. GitHub Actions automatically runs the CI pipeline — compiles, runs unit tests, runs integration tests, SonarCloud quality gate checks (coverage, code smells, vulnerabilities).

3. **Code review.** At least one team member reviews the PR. We check for correctness, backward compatibility, and migration safety.

4. **Merge to develop.** After approval, the PR merges to the develop branch. This triggers a deployment to the staging environment — GitHub Actions builds a Docker image, pushes to ECR, and ECS deploys it to staging. Flyway migrations run on startup.

5. **Staging validation.** QA runs regression tests on staging. We verify the feature works with production-like data.

6. **Release to production.** We merge develop to main (or create a release branch for coordinated releases). GitHub Actions builds the production image, deploys via ECS rolling update across both us-east-1 and us-west-2. The rolling deployment ensures zero downtime — new instances come up healthy before old ones are drained.

7. **Post-deployment verification.** Check New Relic dashboards for error rate spikes, latency changes, and traffic patterns. Verify critical user journeys manually.

**Lesson:** The most important part isn't the tooling — it's the gates. Automated tests, quality checks, and code review catch issues before they reach production. We had a rule: no direct pushes to main, no skipping CI.
