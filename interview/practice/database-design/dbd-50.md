**50.** Recommendation System: Track user preferences, item interactions, and similar item relationships.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### users
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(100) | NOT NULL |
| email | VARCHAR(255) | NOT NULL, UNIQUE |
| created_at | TIMESTAMP | NOT NULL DEFAULT NOW() |

### items
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| category | VARCHAR(100) | |
| tags | VARCHAR[] | (for content-based filtering) |
| created_at | TIMESTAMP | NOT NULL DEFAULT NOW() |

### user_preferences
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| user_id | BIGINT | FK → users(id), NOT NULL |
| category | VARCHAR(100) | NOT NULL |
| weight | DECIMAL(3,2) | NOT NULL DEFAULT 1.00 |
| UNIQUE(user_id, category) | | |

### user_interactions
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| user_id | BIGINT | FK → users(id), NOT NULL |
| item_id | BIGINT | FK → items(id), NOT NULL |
| interaction_type | VARCHAR(20) | NOT NULL (VIEW, CLICK, PURCHASE, RATE, SAVE) |
| rating | SMALLINT | CHECK (rating BETWEEN 1 AND 5) |
| duration_sec | INT | (time spent viewing) |
| created_at | TIMESTAMP | NOT NULL DEFAULT NOW() |

### item_similarities (precomputed)
| Column | Type | Constraints |
|--------|------|-------------|
| item_id_a | BIGINT | FK → items(id), NOT NULL |
| item_id_b | BIGINT | FK → items(id), NOT NULL |
| similarity_score | DECIMAL(5,4) | NOT NULL, CHECK (similarity_score BETWEEN 0 AND 1) |
| algorithm | VARCHAR(30) | NOT NULL (COLLABORATIVE, CONTENT_BASED, HYBRID) |
| computed_at | TIMESTAMP | NOT NULL DEFAULT NOW() |
| PK(item_id_a, item_id_b, algorithm) | | |
| CHECK(item_id_a < item_id_b) | | avoid duplicates |

### user_recommendations (precomputed, served to user)
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| user_id | BIGINT | FK → users(id), NOT NULL |
| item_id | BIGINT | FK → items(id), NOT NULL |
| score | DECIMAL(5,4) | NOT NULL |
| reason | VARCHAR(100) | (e.g., "Similar to items you purchased") |
| generated_at | TIMESTAMP | NOT NULL DEFAULT NOW() |
| is_dismissed | BOOLEAN | NOT NULL DEFAULT FALSE |
| UNIQUE(user_id, item_id) | | |

## Relationships
- users → user_preferences: **1:N**
- users → user_interactions: **1:N**
- items → user_interactions: **1:N**
- items ↔ items: **M:N** via `item_similarities` (self-referencing)
- users → user_recommendations: **1:N**

## ER Diagram (Text)
```
[users] 1───N [user_preferences]
   │
  1:N
   │
[user_interactions] N───1 [items]
                            │ ↑
[user_recommendations]      └─┘ (item_similarities self-ref)
   │
  N:1
   │
[users]
```

## Recommendation Query: "Items similar to what you liked"
```sql
SELECT i.*, ism.similarity_score
FROM items i
JOIN item_similarities ism ON (
    (ism.item_id_a = i.id OR ism.item_id_b = i.id)
)
WHERE (ism.item_id_a IN (:likedItemIds) OR ism.item_id_b IN (:likedItemIds))
  AND i.id NOT IN (:alreadyInteractedIds)
ORDER BY ism.similarity_score DESC
LIMIT 20;
```

## Example & Insight
Netflix/Spotify model uses three layers: (1) `user_interactions` — raw signal data (views, clicks, ratings), (2) `item_similarities` — precomputed offline by ML batch jobs (run nightly), (3) `user_recommendations` — precomputed per user, served instantly. The `CHECK(item_id_a < item_id_b)` constraint avoids storing both (A,B) and (B,A). Index `(user_id, interaction_type, created_at DESC)` on interactions for "recently viewed" features.

## Trade-off
Precomputed recommendations (`user_recommendations`) are fast to serve but stale — a user who just bought a laptop still sees laptop recommendations until the next batch run. Real-time systems use a hybrid: serve precomputed recs but filter out recently purchased items at query time. The interaction log (`user_interactions`) grows fast — partition by `created_at` and archive data older than 90 days to a data warehouse for ML training.
