**45. Security Scanning in CI/CD**
What types of security scanning can you integrate into a CI/CD pipeline? (SAST, DAST, dependency scanning, container scanning)

========== THIS SECTION IS THE ANSWER ==========

Four types: SAST (Static Application Security Testing) scans source code for vulnerabilities before runtime. DAST (Dynamic) tests the running application for vulnerabilities like injection. Dependency scanning checks libraries for known CVEs. Container scanning checks Docker images for OS-level vulnerabilities.

In our project, we used SonarCloud for SAST in every PR (caught SQL injection patterns, hardcoded secrets). Dependabot scanned dependencies weekly and auto-created PRs for vulnerable libraries. ECR image scanning ran on every push. We didn't run DAST in CI (too slow) but ran it weekly against staging as a scheduled job.

The trade-off: more scanning = slower pipeline. SAST added 2 minutes, dependency scanning was nearly instant, but DAST could take 30+ minutes. We placed fast scans early (fail fast) and slow scans as async jobs. The key lesson: don't block the pipeline on low-severity findings — we set the quality gate to fail only on critical and high severity, with a weekly review for medium issues.
