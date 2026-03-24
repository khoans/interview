**11.** Design a database for a blog system. A user can write multiple posts. A post can have multiple comments. A comment belongs to one user. Describe the tables.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### blog_user
| Column | Type | Constraints |
|--------|------|-------------|
| user_id | BIGINT | PK, AUTO_INCREMENT |
| username | VARCHAR(50) | UNIQUE, NOT NULL |
| email | VARCHAR(255) | UNIQUE, NOT NULL |
| password_hash | VARCHAR(255) | NOT NULL |
| created_at | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP |

### post
| Column | Type | Constraints |
|--------|------|-------------|
| post_id | BIGINT | PK, AUTO_INCREMENT |
| user_id | BIGINT | FK → blog_user(user_id), NOT NULL |
| title | VARCHAR(500) | NOT NULL |
| body | TEXT | NOT NULL |
| status | VARCHAR(20) | NOT NULL, DEFAULT 'DRAFT' |
| published_at | TIMESTAMP | NULL |
| created_at | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP |

### comment
| Column | Type | Constraints |
|--------|------|-------------|
| comment_id | BIGINT | PK, AUTO_INCREMENT |
| post_id | BIGINT | FK → post(post_id), NOT NULL |
| user_id | BIGINT | FK → blog_user(user_id), NOT NULL |
| body | TEXT | NOT NULL |
| created_at | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP |
| parent_comment_id | BIGINT | FK → comment(comment_id), NULL |

## Relationships
- **User → Post**: 1:N (a user writes many posts)
- **Post → Comment**: 1:N (a post has many comments)
- **User → Comment**: 1:N (a comment belongs to one user)
- **Comment → Comment**: self-referential 1:N (for threaded replies)

## ER Diagram
```
+-----------+       +----------+       +-----------+
| blog_user |       |   post   |       |  comment  |
+-----------+       +----------+       +-----------+
| user_id   |──1:N──| post_id  |──1:N──|comment_id |
| username  |       | user_id  |       | post_id   |
| email     |  1:N  | title    |       | user_id   |──N:1──blog_user
| pwd_hash  |──────>| body     |       | body      |
| created_at|       | status   |       | created_at|
+-----------+       |published |       |parent_id  |──N:1──comment (self)
                    | created_at|      +-----------+
                    +----------+
```

## Key Design Insights
- **All three tables are 1:N** — no junction tables needed. This is a simpler pattern than M:N questions, but the self-referencing FK on `comment.parent_comment_id` adds depth. It enables threaded/nested replies like Reddit. Without it, you can only have flat comments.
- **Named `blog_user`** not `user` because `USER` is a reserved keyword in most databases. Alternatively, quote it as `"user"`, but that leads to constant quoting headaches.
- **Trade-off**: `parent_comment_id` creates a tree structure which is notoriously hard to query in SQL (recursive CTEs needed for deep nesting). For most blogs, limiting reply depth to 2-3 levels and using a `path` column (materialized path pattern like `/1/5/12/`) is more performant than recursive queries. Index on `(post_id, created_at)` for loading comments chronologically.
