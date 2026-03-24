**97.** How did you handle testing database migrations? Did you test rollbacks?

========== THIS SECTION IS THE ANSWER ==========

We used Flyway for all database migrations and tested them in CI before they reached staging or production.

Every migration had a corresponding test in our integration test suite. Testcontainers spun up a clean PostgreSQL instance, Flyway ran all migrations from V1 through the latest, and then our integration tests verified that the application code worked with the new schema. For rollback testing, we wrote separate "undo" migration scripts (Flyway's undo feature in the paid version; we used a naming convention: U__description.sql alongside V__description.sql). Before applying a migration to production, we tested the rollback path in staging: apply migration, verify app works, rollback migration, verify app still works with the old schema. For risky migrations (adding NOT NULL constraints, renaming columns), we used a multi-phase approach: Phase 1 — add the new column (nullable), deploy code that writes to both old and new columns. Phase 2 — backfill data. Phase 3 — remove old column. Each phase was a separate Flyway migration deployed independently.

Lesson: never deploy a destructive migration (DROP COLUMN, ALTER TYPE) and a code change in the same release. If the code deployment fails and rolls back, you still have the schema change and the old code can't run. Always make migrations backward-compatible.
