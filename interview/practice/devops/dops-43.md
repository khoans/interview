**43. Secrets Management**
How do you manage secrets (API keys, database passwords) in a DevOps pipeline? Compare solutions: environment variables, Vault, AWS Secrets Manager, etc.

========== THIS SECTION IS THE ANSWER ==========

Secrets should never be in code or config files. Options: environment variables (simple but no rotation/audit), HashiCorp Vault (powerful, self-hosted, supports dynamic secrets), AWS Secrets Manager (managed, integrates with AWS services, supports auto-rotation), AWS Parameter Store (simpler, cheaper, good for non-critical config).

In our project, we used AWS Secrets Manager for database credentials and API keys. ECS task definitions referenced secrets by ARN, and AWS injected them as environment variables at container startup. Secrets Manager auto-rotated the RDS password every 30 days without any application changes — the new password was transparently picked up on next container restart.

The trade-off: Secrets Manager costs $0.40/secret/month and adds a slight startup delay (API call to fetch secrets). Parameter Store is free for standard parameters but doesn't support auto-rotation. For our scale, the cost was negligible and auto-rotation was a security compliance requirement, so Secrets Manager was the clear choice.
