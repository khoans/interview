**32.** How does your CI/CD pipeline work? What stages does it have?

========== THIS SECTION IS THE ANSWER ==========

Our CI/CD pipeline ran on GitHub Actions with these stages:

**Stage 1 — Build & Unit Tests.** Triggered on every push to any branch. Maven compiles the code, runs unit tests (~500 tests, completes in about 3 minutes). If this fails, the PR is blocked.

**Stage 2 — Integration Tests.** Runs after unit tests pass. Spins up a PostgreSQL container via Docker Compose, runs integration tests that hit the real database. This catches issues like incorrect SQL, missing Flyway migrations, and JPA mapping errors.

**Stage 3 — Code Quality.** SonarCloud analyzes the code for coverage (minimum 80% for new code), code smells, security vulnerabilities, and duplication. A quality gate failure blocks the merge.

**Stage 4 — Build Docker Image.** On merge to develop or main, the pipeline builds a Docker image and pushes it to AWS ECR. Images are tagged with the git commit hash for traceability.

**Stage 5 — Deploy to Staging.** For develop branch merges, ECS pulls the new image and does a rolling deployment to staging. Flyway migrations run automatically on application startup.

**Stage 6 — Deploy to Production.** For main branch merges, the same image (already tested in staging) is deployed to production ECS clusters in both us-east-1 and us-west-2. Rolling deployment with health checks ensures zero downtime.

**Stage 7 — Smoke Tests.** Post-deployment, automated smoke tests hit critical endpoints (search, vehicle details, health checks) to verify the deployment is healthy.

**Trade-off:** Our pipeline took about 12 minutes end-to-end. Some teams wanted faster feedback, but we prioritized thoroughness. A 12-minute pipeline that catches issues is better than a 3-minute pipeline that lets bugs reach production.
