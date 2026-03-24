**29.** How do you test database changes before deploying to production?

========== THIS SECTION IS THE ANSWER ==========

We had a multi-stage validation process for any database change.

First, **local development.** Flyway migration scripts ran against a local PostgreSQL Docker container. I wrote the migration, verified it applied cleanly, and tested that both the old and new code worked against the updated schema (backward compatibility check).

Second, **code review.** Every migration script went through PR review. Reviewers checked for destructive operations (DROP, RENAME), missing indexes on new columns, and whether the migration was backward-compatible with the current production code. We had a checklist: no locking ALTER TABLE on large tables during peak hours, no NOT NULL without default on existing columns.

Third, **staging environment.** Staging had a copy of production data (anonymized). The migration ran against realistic data volumes, so we could catch issues like "this ALTER TABLE takes 20 minutes on 8 million rows" that you'd never see on an empty local database. We also ran integration tests against the updated schema.

Fourth, **production deployment.** Flyway ran the migration on startup during ECS rolling deployment. We deployed during low-traffic windows for risky migrations. The previous version's instances kept serving on the old schema while the new version came up.

For large data migrations (backfills), we ran them as separate scripts with batching and progress logging, never in the Flyway migration itself.

**Lesson:** The staging database size matters. A migration that runs in 200ms on a test database can lock a production table for minutes. Always test with production-scale data volumes.
