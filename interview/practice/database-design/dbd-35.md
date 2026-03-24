**35.** Task Management: Projects, Tasks, Users, Subtasks, Status, Priority. A project can have multiple tasks. A task can be assigned to multiple users. A task can have multiple subtasks. Track task status and priority.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### users
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(100) | NOT NULL |
| email | VARCHAR(255) | NOT NULL, UNIQUE |

### projects
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| description | TEXT | |
| owner_id | BIGINT | FK → users(id), NOT NULL |
| status | VARCHAR(20) | NOT NULL DEFAULT 'ACTIVE' |
| created_at | TIMESTAMP | NOT NULL DEFAULT NOW() |

### tasks
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| project_id | BIGINT | FK → projects(id), NOT NULL |
| parent_task_id | BIGINT | FK → tasks(id), NULL |
| title | VARCHAR(300) | NOT NULL |
| description | TEXT | |
| status | VARCHAR(20) | NOT NULL DEFAULT 'TODO' |
| priority | VARCHAR(10) | NOT NULL DEFAULT 'MEDIUM' |
| due_date | DATE | |
| created_at | TIMESTAMP | NOT NULL DEFAULT NOW() |
| updated_at | TIMESTAMP | NOT NULL DEFAULT NOW() |

### task_assignees (M:N — tasks ↔ users)
| Column | Type | Constraints |
|--------|------|-------------|
| task_id | BIGINT | FK → tasks(id), NOT NULL |
| user_id | BIGINT | FK → users(id), NOT NULL |
| assigned_at | TIMESTAMP | NOT NULL DEFAULT NOW() |
| PK(task_id, user_id) | | |

## Relationships
- projects → tasks: **1:N**
- tasks → tasks (subtasks): **1:N** self-referencing via `parent_task_id`
- tasks ↔ users: **M:N** via `task_assignees`

## ER Diagram (Text)
```
[users] 1───N [projects]
   │               │
  M:N             1:N
   │               │
[task_assignees]──[tasks]
                    │ ↑
                    └─┘ (parent_task_id self-ref)
```

## Example & Insight
Self-referencing `parent_task_id` is the Jira/ClickUp pattern — subtasks are just tasks with a parent. This supports unlimited nesting. Status enum: TODO → IN_PROGRESS → IN_REVIEW → DONE. Priority enum: CRITICAL, HIGH, MEDIUM, LOW. Index `(project_id, status, priority)` for board views and `(parent_task_id)` for subtask queries.

## Trade-off
Self-referencing allows unlimited depth but makes queries like "get all descendants" require recursive CTEs (`WITH RECURSIVE`). If you only need one level of subtasks (like most real tools), consider adding a CHECK constraint `WHERE parent_task_id IS NULL OR NOT EXISTS nested parent` — or just enforce at application level. Keeps queries simpler.
