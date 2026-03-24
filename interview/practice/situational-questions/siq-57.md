**57.** How did you manage API keys and secrets in your application? Where do you store them?

========== THIS SECTION IS THE ANSWER ==========

AWS Secrets Manager for production secrets, injected at runtime via environment variables. Never in code, never in config files committed to Git.

On the automotive marketplace, all sensitive values — database credentials, third-party API keys (Solr, AWS Bedrock), OAuth2 client secrets — were stored in AWS Secrets Manager. Our ECS task definitions referenced these secrets, and they were injected as environment variables when containers started. For local development, we used a .env file (gitignored) that mirrored the secret structure. Our GitHub Actions CI/CD pipeline pulled secrets from AWS Secrets Manager during deployment using IAM role-based access — no long-lived credentials stored in GitHub. We also rotated database passwords quarterly through Secrets Manager's rotation feature, and the application picked up new credentials automatically on the next ECS task restart.

The trade-off: Secrets Manager adds a small cost (~$0.40/secret/month) and a dependency — if Secrets Manager is down, services can't start. We mitigated this by caching secrets in memory with a 1-hour TTL. The lesson: the cost of proper secret management is trivial compared to the cost of a credential leak in a system serving 30M users.
