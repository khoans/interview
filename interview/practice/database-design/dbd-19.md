**19.** Design a database for a social media platform. A user can post multiple photos. A photo can have multiple comments. A user can follow multiple users. Describe the tables.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### app_user
| Column | Type | Constraints |
|--------|------|-------------|
| user_id | BIGINT | PK, AUTO_INCREMENT |
| username | VARCHAR(50) | UNIQUE, NOT NULL |
| email | VARCHAR(255) | UNIQUE, NOT NULL |
| display_name | VARCHAR(100) | NULL |
| bio | VARCHAR(500) | NULL |
| created_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP |

### photo
| Column | Type | Constraints |
|--------|------|-------------|
| photo_id | BIGINT | PK, AUTO_INCREMENT |
| user_id | BIGINT | FK → app_user(user_id), NOT NULL |
| image_url | VARCHAR(500) | NOT NULL |
| caption | VARCHAR(2000) | NULL |
| created_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP |

### comment
| Column | Type | Constraints |
|--------|------|-------------|
| comment_id | BIGINT | PK, AUTO_INCREMENT |
| photo_id | BIGINT | FK → photo(photo_id), NOT NULL |
| user_id | BIGINT | FK → app_user(user_id), NOT NULL |
| body | TEXT | NOT NULL |
| created_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP |

### follow (junction — User ↔ User)
| Column | Type | Constraints |
|--------|------|-------------|
| follower_id | BIGINT | PK, FK → app_user(user_id) |
| following_id | BIGINT | PK, FK → app_user(user_id) |
| followed_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP |
| CHECK | | (follower_id != following_id) |

## Relationships
- **User → Photo**: 1:N
- **Photo → Comment**: 1:N
- **User → Comment**: 1:N (commenter)
- **User ↔ User**: M:N self-referential via `follow`

## ER Diagram
```
+----------+       +----------+       +----------+
| app_user |       |  photo   |       |  comment |
+----------+       +----------+       +----------+
| user_id  |──1:N──| photo_id |──1:N──|comment_id|
| username |       | user_id  |       | photo_id |
| email    |       | image_url|       | user_id  |──N:1──app_user
|display_nm|       | caption  |       | body     |
| bio      |       |created_at|       |created_at|
|created_at|       +----------+       +----------+
+----+-----+
     |
     | M:N (self-referential)
     |
+----+------+
|  follow   |
+-----------+
|follower_id|──FK──app_user
|following_id|──FK──app_user
|followed_at|
+-----------+
```

## Key Design Insights
- **Self-referential M:N** is the signature pattern here. The `follow` table has two FKs pointing to the same `app_user` table. This is directional (A follows B does not mean B follows A), unlike a friendship model which would be bidirectional. The composite PK `(follower_id, following_id)` prevents duplicate follows.
- **CHECK constraint `follower_id != following_id`** prevents a user from following themselves. This is a database-level guard that application code should also enforce, but defense in depth matters.
- **Trade-off**: The feed query ("show me photos from people I follow, sorted by time") is the most expensive query: `SELECT p.* FROM photo p WHERE p.user_id IN (SELECT following_id FROM follow WHERE follower_id = :me) ORDER BY p.created_at DESC`. At scale (millions of users), this becomes a fan-out problem. Solutions: denormalized feed table (write-time fan-out like Twitter) or caching. Index on `follow(follower_id)` and `photo(user_id, created_at DESC)` is essential.
