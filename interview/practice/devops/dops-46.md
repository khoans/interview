**46. Database Migrations in CI/CD**
How do you handle database schema migrations in an automated pipeline? What tools do you use? How do you handle rollbacks?

========== THIS SECTION IS THE ANSWER ==========

Migrations are version-controlled SQL scripts that evolve your database schema. Tools like Flyway or Liquibase track which migrations have run. In CI/CD, migrations run automatically during deployment — after the database is healthy but before the new application starts.

In our project, we used Flyway with Spring Boot. Migration scripts were named `V1__create_users.sql`, `V2__add_email_column.sql`, etc. The pipeline ran `flyway migrate` against staging first, then production. Each migration was forward-only — we never used Flyway's undo feature in production because it's unreliable for destructive changes.

The key rule: every migration must be backward-compatible with the current running application. If you need to rename a column: step 1 — add new column, deploy app that reads both. Step 2 — migrate data. Step 3 — deploy app that reads only new column. Step 4 — drop old column. We once skipped this and renamed directly — rollback was impossible because the old app version couldn't find the old column name.
