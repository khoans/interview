**47.** Review and Rating: Users, Products, Reviews, Votes. A user can review multiple products. A product can have multiple reviews. A review can have multiple helpful votes.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### users
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(100) | NOT NULL |
| email | VARCHAR(255) | NOT NULL, UNIQUE |

### products
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| avg_rating | DECIMAL(2,1) | DEFAULT 0 |
| review_count | INT | DEFAULT 0 |

### reviews
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| user_id | BIGINT | FK → users(id), NOT NULL |
| product_id | BIGINT | FK → products(id), NOT NULL |
| rating | SMALLINT | NOT NULL, CHECK (rating BETWEEN 1 AND 5) |
| title | VARCHAR(200) | |
| body | TEXT | |
| helpful_count | INT | NOT NULL DEFAULT 0 |
| verified_purchase | BOOLEAN | NOT NULL DEFAULT FALSE |
| created_at | TIMESTAMP | NOT NULL DEFAULT NOW() |
| updated_at | TIMESTAMP | |
| UNIQUE(user_id, product_id) | | |

### review_votes
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| review_id | BIGINT | FK → reviews(id), NOT NULL |
| user_id | BIGINT | FK → users(id), NOT NULL |
| is_helpful | BOOLEAN | NOT NULL |
| created_at | TIMESTAMP | NOT NULL DEFAULT NOW() |
| UNIQUE(review_id, user_id) | | |

## Relationships
- users → reviews: **1:N**
- products → reviews: **1:N**
- reviews → review_votes: **1:N**
- users → review_votes: **1:N** (a user votes on reviews)

## ER Diagram (Text)
```
[users] 1───N [reviews] N───1 [products]
   │              │
   │             1:N
   │              │
   └───N [review_votes]
```

## Example & Insight
Amazon-style design. `avg_rating` and `review_count` on products are denormalized — recalculate on review insert/delete: `UPDATE products SET avg_rating = (SELECT AVG(rating) FROM reviews WHERE product_id = :id), review_count = (SELECT COUNT(*) FROM reviews WHERE product_id = :id) WHERE id = :id`. The `UNIQUE(user_id, product_id)` prevents duplicate reviews. Sort reviews by `helpful_count DESC` for "most helpful" view — index `(product_id, helpful_count DESC)`.

## Trade-off
Denormalized `avg_rating` can become stale if updates fail. Alternatives: (1) materialized view refreshed periodically, (2) computed column (MySQL generated columns / PostgreSQL `GENERATED ALWAYS`), (3) application-level cache. For product pages with high read traffic, denormalization is worth the consistency trade-off — just update it transactionally with the review insert.
