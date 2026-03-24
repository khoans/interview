**26.** Subscription Service: Users, Magazines, Issues, Articles. A user can subscribe to multiple magazines. A magazine can have multiple subscribers. An issue can contain multiple articles.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### users
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| email | VARCHAR(255) | NOT NULL, UNIQUE |
| full_name | VARCHAR(100) | NOT NULL |
| created_at | TIMESTAMP | NOT NULL, DEFAULT NOW() |

### magazines
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| title | VARCHAR(200) | NOT NULL |
| publisher | VARCHAR(200) | |
| frequency | VARCHAR(20) | NOT NULL (WEEKLY, MONTHLY, QUARTERLY) |
| created_at | TIMESTAMP | NOT NULL, DEFAULT NOW() |

### subscriptions (M:N junction — users ↔ magazines)
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| user_id | BIGINT | FK → users(id), NOT NULL |
| magazine_id | BIGINT | FK → magazines(id), NOT NULL |
| start_date | DATE | NOT NULL |
| end_date | DATE | NOT NULL |
| status | VARCHAR(20) | NOT NULL (ACTIVE, EXPIRED, CANCELLED) |
| UNIQUE(user_id, magazine_id, start_date) | | |

### issues
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| magazine_id | BIGINT | FK → magazines(id), NOT NULL |
| issue_number | INT | NOT NULL |
| publish_date | DATE | NOT NULL |
| cover_url | VARCHAR(500) | |
| UNIQUE(magazine_id, issue_number) | | |

### articles
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| issue_id | BIGINT | FK → issues(id), NOT NULL |
| title | VARCHAR(300) | NOT NULL |
| author | VARCHAR(100) | |
| content | TEXT | NOT NULL |
| page_number | INT | |

## Relationships
- users ↔ magazines: **M:N** via `subscriptions`
- magazines → issues: **1:N**
- issues → articles: **1:N**

## ER Diagram (Text)
```
[users] 1───M [subscriptions] M───1 [magazines]
                                        │
                                       1:N
                                        │
                                    [issues]
                                        │
                                       1:N
                                        │
                                   [articles]
```

## Example & Insight
Netflix-style model — subscription table tracks periods, not just a boolean flag. This lets you query "active subscribers this month" with `WHERE status = 'ACTIVE' AND end_date >= CURRENT_DATE`. Index on `(magazine_id, status)` for fast subscriber counts.

## Trade-off
Storing `status` as a column means you need a scheduled job to flip ACTIVE → EXPIRED. Alternative: compute status from `end_date` at query time (no stale data, but slower queries on large tables).
