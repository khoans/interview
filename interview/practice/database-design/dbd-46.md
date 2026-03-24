**46.** Notification System: Users, Notifications, Types, Read Status. A user can receive multiple notifications. A notification can be of multiple types. Track read/unread status.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### users
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(100) | NOT NULL |
| email | VARCHAR(255) | NOT NULL, UNIQUE |

### notification_types
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| code | VARCHAR(50) | NOT NULL, UNIQUE |
| name | VARCHAR(100) | NOT NULL |
| template | TEXT | (e.g., "{{actor}} commented on your {{entity}}") |
| channel | VARCHAR(20) | NOT NULL DEFAULT 'IN_APP' |

### notifications
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| type_id | BIGINT | FK → notification_types(id), NOT NULL |
| actor_id | BIGINT | FK → users(id) |
| entity_type | VARCHAR(50) | (e.g., 'order', 'comment') |
| entity_id | BIGINT | |
| title | VARCHAR(300) | NOT NULL |
| body | TEXT | |
| data | JSONB | (extra payload for deep linking) |
| created_at | TIMESTAMP | NOT NULL DEFAULT NOW() |

### user_notifications (delivery + read tracking)
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| user_id | BIGINT | FK → users(id), NOT NULL |
| notification_id | BIGINT | FK → notifications(id), NOT NULL |
| is_read | BOOLEAN | NOT NULL DEFAULT FALSE |
| read_at | TIMESTAMP | |
| delivered_at | TIMESTAMP | |
| UNIQUE(user_id, notification_id) | | |

### user_notification_preferences
| Column | Type | Constraints |
|--------|------|-------------|
| user_id | BIGINT | FK → users(id), NOT NULL |
| type_id | BIGINT | FK → notification_types(id), NOT NULL |
| enabled | BOOLEAN | NOT NULL DEFAULT TRUE |
| PK(user_id, type_id) | | |

## Relationships
- notification_types → notifications: **1:N**
- notifications → user_notifications: **1:N** (fan-out to recipients)
- users → user_notifications: **1:N**
- users ↔ notification_types: **M:N** via `user_notification_preferences`

## ER Diagram (Text)
```
[users] 1───N [user_notifications] N───1 [notifications] N───1 [notification_types]
   │                                                                    │
  M:N via [user_notification_preferences] ──────────────────────────────┘
```

## Example & Insight
The fan-out pattern: one event (e.g., "new comment on post") creates ONE notification row, then N `user_notifications` rows for each subscriber. Unread count query: `SELECT COUNT(*) FROM user_notifications WHERE user_id = :id AND is_read = FALSE`. Cache this count in Redis and decrement on read. Index `(user_id, is_read, created_at DESC)` for the notification inbox.

## Trade-off
For high-volume systems (millions of users), fan-out-on-write (creating user_notifications at send time) causes write amplification. Alternative: fan-out-on-read — store only the notification and compute "should user X see this?" at read time. Twitter uses fan-out-on-write for most users but fan-out-on-read for celebrities with millions of followers. Start with fan-out-on-write; it is simpler and fast enough for most apps.
