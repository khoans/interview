**45.** RBAC: Users, Roles, Permissions. A user can have multiple roles. A role can have multiple permissions. A permission can be assigned to multiple roles.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### users
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| username | VARCHAR(50) | NOT NULL, UNIQUE |
| email | VARCHAR(255) | NOT NULL, UNIQUE |
| password_hash | VARCHAR(255) | NOT NULL |
| is_active | BOOLEAN | NOT NULL DEFAULT TRUE |
| created_at | TIMESTAMP | NOT NULL DEFAULT NOW() |

### roles
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(50) | NOT NULL, UNIQUE |
| description | VARCHAR(200) | |

### permissions
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| resource | VARCHAR(100) | NOT NULL (e.g., 'orders', 'users') |
| action | VARCHAR(20) | NOT NULL (READ, CREATE, UPDATE, DELETE) |
| UNIQUE(resource, action) | | |

### user_roles (M:N — users ↔ roles)
| Column | Type | Constraints |
|--------|------|-------------|
| user_id | BIGINT | FK → users(id), NOT NULL |
| role_id | BIGINT | FK → roles(id), NOT NULL |
| granted_at | TIMESTAMP | NOT NULL DEFAULT NOW() |
| granted_by | BIGINT | FK → users(id) |
| PK(user_id, role_id) | | |

### role_permissions (M:N — roles ↔ permissions)
| Column | Type | Constraints |
|--------|------|-------------|
| role_id | BIGINT | FK → roles(id), NOT NULL |
| permission_id | BIGINT | FK → permissions(id), NOT NULL |
| PK(role_id, permission_id) | | |

## Relationships
- users ↔ roles: **M:N** via `user_roles`
- roles ↔ permissions: **M:N** via `role_permissions`

## ER Diagram (Text)
```
[users] 1───M [user_roles] M───1 [roles] 1───M [role_permissions] M───1 [permissions]
```

## Authorization Check Query
```sql
SELECT DISTINCT p.resource, p.action
FROM permissions p
JOIN role_permissions rp ON p.id = rp.permission_id
JOIN user_roles ur ON rp.role_id = ur.role_id
WHERE ur.user_id = :userId;
```

## Example & Insight
Spring Security maps this directly: `@PreAuthorize("hasAuthority('orders:UPDATE')")`. The `resource:action` pattern (e.g., `orders:READ`, `users:DELETE`) is how AWS IAM and most RBAC systems work. Cache user permissions in Redis/JWT — this query runs once at login, not per request. Typical roles: ADMIN (all permissions), MANAGER (read + update), VIEWER (read only).

## Trade-off
Pure RBAC is simple but lacks context — "a manager can only update orders in their department" requires ABAC (Attribute-Based Access Control) on top. Options: (1) add a `scope` column to `user_roles` (e.g., `department_id`), (2) implement row-level checks in code. Start with RBAC, add attribute checks in the service layer when needed.
