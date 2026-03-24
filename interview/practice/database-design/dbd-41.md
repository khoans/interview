**41.** Multi-tenant SaaS: Design for tenant isolation. How to structure tables to support multi-tenancy.



========== THIS SECTION IS THE ANSWER ==========

## Approach: Shared Database, Shared Schema with tenant_id

This is the most common approach for SaaS — all tenants share the same tables, isolated by a `tenant_id` column.

## Table Definitions

### tenants
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| slug | VARCHAR(50) | NOT NULL, UNIQUE |
| plan | VARCHAR(20) | NOT NULL DEFAULT 'FREE' |
| is_active | BOOLEAN | NOT NULL DEFAULT TRUE |
| created_at | TIMESTAMP | NOT NULL DEFAULT NOW() |

### users
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| tenant_id | BIGINT | FK → tenants(id), NOT NULL |
| email | VARCHAR(255) | NOT NULL |
| name | VARCHAR(100) | NOT NULL |
| role | VARCHAR(20) | NOT NULL DEFAULT 'MEMBER' |
| UNIQUE(tenant_id, email) | | |

### projects (example business entity)
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| tenant_id | BIGINT | FK → tenants(id), NOT NULL |
| name | VARCHAR(200) | NOT NULL |
| created_by | BIGINT | FK → users(id), NOT NULL |
| created_at | TIMESTAMP | NOT NULL DEFAULT NOW() |

### tasks (example business entity)
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| tenant_id | BIGINT | FK → tenants(id), NOT NULL |
| project_id | BIGINT | FK → projects(id), NOT NULL |
| title | VARCHAR(300) | NOT NULL |
| status | VARCHAR(20) | NOT NULL DEFAULT 'TODO' |

## Multi-tenancy Strategies Compared

| Strategy | Isolation | Cost | Complexity | Use When |
|----------|-----------|------|------------|----------|
| **Shared schema + tenant_id** | Low | Low | Low | Most SaaS (Slack, Jira) |
| **Schema per tenant** | Medium | Medium | Medium | Compliance needs |
| **Database per tenant** | High | High | High | Enterprise/regulated |

## Key Design Rules

1. **Every table gets `tenant_id`** — no exceptions for business data
2. **Composite unique constraints** — `UNIQUE(tenant_id, email)` not just `UNIQUE(email)`
3. **Every query includes `WHERE tenant_id = ?`** — enforce via Row Level Security (RLS) or application middleware
4. **Indexes always lead with `tenant_id`** — e.g., `INDEX(tenant_id, status)` not `INDEX(status)`

## ER Diagram (Text)
```
[tenants] 1───N [users]
    │              │
   1:N            N:1
    │              │
[projects]────────┘
    │
   1:N
    │
 [tasks] (also has tenant_id)
```

## Example & Insight — PostgreSQL RLS
```sql
-- Row Level Security prevents cross-tenant access at DB level
ALTER TABLE projects ENABLE ROW LEVEL SECURITY;
CREATE POLICY tenant_isolation ON projects
  USING (tenant_id = current_setting('app.current_tenant')::BIGINT);

-- Set tenant context per request in your middleware
SET app.current_tenant = '42';
SELECT * FROM projects;  -- Only sees tenant 42's projects
```
This is how Supabase and Citus handle multi-tenancy. Even if your app code has a bug, RLS prevents data leaks.

## Trade-off
Shared schema is cheapest and simplest but makes tenant data migration (export one tenant's data) harder — you need `WHERE tenant_id = ?` on every table. Schema-per-tenant makes migration trivial (`pg_dump -n tenant_42`) but increases operational overhead (thousands of schemas = slow migrations, DDL changes must apply to all schemas).
