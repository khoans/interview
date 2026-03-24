**44.** Audit Logging System: Track who made what changes to which records and when.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### audit_logs
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| table_name | VARCHAR(100) | NOT NULL |
| record_id | VARCHAR(50) | NOT NULL |
| action | VARCHAR(10) | NOT NULL (INSERT, UPDATE, DELETE) |
| user_id | BIGINT | NOT NULL |
| user_ip | VARCHAR(45) | |
| old_values | JSONB | (NULL for INSERT) |
| new_values | JSONB | (NULL for DELETE) |
| changed_fields | VARCHAR[] | (list of modified columns) |
| created_at | TIMESTAMP | NOT NULL DEFAULT NOW() |

### Key Indexes
```sql
CREATE INDEX idx_audit_table_record ON audit_logs(table_name, record_id);
CREATE INDEX idx_audit_user ON audit_logs(user_id, created_at DESC);
CREATE INDEX idx_audit_created ON audit_logs(created_at);
CREATE INDEX idx_audit_action ON audit_logs(table_name, action, created_at);
```

## Implementation: PostgreSQL Trigger

```sql
CREATE OR REPLACE FUNCTION audit_trigger_func()
RETURNS TRIGGER AS $$
BEGIN
  IF TG_OP = 'INSERT' THEN
    INSERT INTO audit_logs(table_name, record_id, action, user_id, new_values, created_at)
    VALUES (TG_TABLE_NAME, NEW.id::TEXT, 'INSERT',
            current_setting('app.user_id')::BIGINT,
            to_jsonb(NEW), NOW());
  ELSIF TG_OP = 'UPDATE' THEN
    INSERT INTO audit_logs(table_name, record_id, action, user_id, old_values, new_values, created_at)
    VALUES (TG_TABLE_NAME, NEW.id::TEXT, 'UPDATE',
            current_setting('app.user_id')::BIGINT,
            to_jsonb(OLD), to_jsonb(NEW), NOW());
  ELSIF TG_OP = 'DELETE' THEN
    INSERT INTO audit_logs(table_name, record_id, action, user_id, old_values, created_at)
    VALUES (TG_TABLE_NAME, OLD.id::TEXT, 'DELETE',
            current_setting('app.user_id')::BIGINT,
            to_jsonb(OLD), NOW());
  END IF;
  RETURN COALESCE(NEW, OLD);
END;
$$ LANGUAGE plpgsql;

-- Apply to any table
CREATE TRIGGER audit_orders
  AFTER INSERT OR UPDATE OR DELETE ON orders
  FOR EACH ROW EXECUTE FUNCTION audit_trigger_func();
```

## ER Diagram (Text)
```
[any_table] ──trigger──> [audit_logs]
                              │
                          (table_name,
                           record_id,
                           action,
                           old/new JSONB)
```

## Example & Insight
Using JSONB for old/new values is the key — it works for ANY table without schema coupling. To answer "who changed order #123's status?": `SELECT * FROM audit_logs WHERE table_name = 'orders' AND record_id = '123' AND new_values->>'status' IS DISTINCT FROM old_values->>'status' ORDER BY created_at`. Spring Data JPA alternative: use `@EntityListeners` with Hibernate Envers for automatic audit.

## Trade-off
Audit logs grow fast — a busy table with 1M updates/day = 1M audit rows/day. Solutions: (1) partition by `created_at` monthly, (2) archive old partitions to cold storage (S3), (3) use a separate database/schema for audit. Never delete audit logs from the main table without archiving first — compliance requires retention (typically 7 years for finance).
