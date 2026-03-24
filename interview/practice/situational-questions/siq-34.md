**34.** How do you manage different configurations for dev, staging, and production?

========== THIS SECTION IS THE ANSWER ==========

We used a combination of Spring profiles and AWS infrastructure for configuration management.

**Spring profiles** (application-dev.yml, application-staging.yml, application-prod.yml) handled non-sensitive environment-specific settings like database URLs, connection pool sizes, logging levels, and feature flags. These were committed to the repository since they contained no secrets.

**AWS Secrets Manager** stored sensitive values — database credentials, API keys, third-party service tokens. The Spring Boot application loaded secrets at startup using the AWS SDK. Each environment had its own set of secrets with identical key names but different values, referenced by environment prefix (e.g., /dev/db-password, /prod/db-password).

**AWS Systems Manager Parameter Store** handled operational parameters that might need to change without redeployment — like feature flag overrides, rate limits, and cache TTLs. The application polled Parameter Store periodically or we used Spring Cloud AWS to refresh values.

**Environment variables** in ECS task definitions set SPRING_PROFILES_ACTIVE=prod and the AWS region, which bootstrapped everything else.

The key rule: **never put secrets in application.yml files or environment variables in the CI pipeline.** I saw teams embed database passwords in GitHub Actions secrets and inject them as environment variables — that creates a blast radius where anyone with CI access sees production credentials.

**Lesson:** Configuration should be layered — committed config for non-sensitive defaults, secrets manager for credentials, parameter store for runtime tuning. Keep the number of environment-specific differences as small as possible to reduce "works in staging, fails in prod" surprises.
