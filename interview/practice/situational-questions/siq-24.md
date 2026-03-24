**24.** How do you handle database migrations in production without downtime?

========== THIS SECTION IS THE ANSWER ==========

We used Flyway for versioned migration scripts, and the key principle was making every migration backward-compatible with the currently running code.

The approach was expand-and-contract. Instead of renaming or dropping a column in one release, we'd do it in two phases:

Phase 1 (Expand): Add the new column, deploy code that writes to both old and new columns, backfill existing data. The old code still works because the old column is untouched.

Phase 2 (Contract): After all instances run the new code, deploy the next release that only reads/writes the new column. Then a later migration drops the old column.

For example, when we needed to split a full_name column into first_name and last_name in the dealer table, we first added the two new columns, deployed code that wrote to all three, ran a backfill script during low traffic, verified data integrity, then deployed code that only used the new columns, and finally dropped full_name in a subsequent release.

Migrations ran automatically during deployment — Flyway executed pending scripts on application startup before the service registered with the load balancer. ECS rolling deployments ensured old instances kept serving while new instances started up with the migration.

**Lesson:** Never make destructive schema changes (DROP, RENAME, NOT NULL on existing column) in the same release as the code change. Always give yourself a rollback window where both old and new code can work against the same schema.
