**35.** How do you handle database migration scripts in your deployment pipeline?

========== THIS SECTION IS THE ANSWER ==========

We used Flyway integrated directly into the Spring Boot application. Migration scripts were versioned files (V1__create_dealer_table.sql, V2__add_listing_status_column.sql) committed alongside the application code in the same repository, so a code change and its schema change were always reviewed and deployed together.

**In the pipeline:** Flyway ran automatically on application startup, before the service registered with the load balancer. During an ECS rolling deployment, the new instance executed pending migrations, then started serving traffic. Old instances continued on the previous code — this only worked because we enforced backward-compatible migrations.

**Safeguards we had in place:**
- PR reviews specifically checked migration scripts for destructive operations
- Staging environment had production-scale data, so slow migrations were caught before production
- Each migration was idempotent where possible — using IF NOT EXISTS for index creation, for example
- We separated schema migrations (Flyway) from data migrations (custom scripts) because backfilling millions of rows in a Flyway migration would block application startup

**Rollback strategy:** Flyway doesn't support automatic rollback. If a migration caused issues, we wrote a new forward migration to undo the change (e.g., V3__revert_listing_status_column.sql). This was safer than manual DDL in production.

**Lesson:** Keep migrations small and forward-only. One structural change per migration file. Never combine "add column" and "backfill data" in the same script — the backfill could take minutes on large tables and block your deployment.
