**2. CI/CD Pipeline Stages**
Describe the typical stages in a CI/CD pipeline. What happens in each stage and why is the order important?

========== THIS SECTION IS THE ANSWER ==========

Typical stages: Source → Build → Test → Security Scan → Deploy to Staging → Integration Test → Approval → Deploy to Production. Order matters because you want to fail fast — cheap checks (lint, compile) run first, expensive checks (integration tests, security scans) run later.

In our project, the pipeline was: checkout → compile Java with Maven → unit tests → SonarCloud quality gate → build Docker image → push to ECR → deploy to staging → run smoke tests → manual approval → deploy to production (us-east-1 first, then us-west-2).

The lesson: we originally had security scanning at the end, which meant developers waited 20 minutes before finding a vulnerability. Moving SAST earlier in the pipeline cut feedback time significantly. Fast-fail ordering saves developer time.
