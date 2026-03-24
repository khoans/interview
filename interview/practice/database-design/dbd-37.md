**37.** Crowdfunding: Users, Campaigns, Backers, Reward Tiers. A user can create multiple campaigns. A user can back multiple campaigns. A campaign can have multiple reward tiers.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### users
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(100) | NOT NULL |
| email | VARCHAR(255) | NOT NULL, UNIQUE |
| created_at | TIMESTAMP | NOT NULL DEFAULT NOW() |

### campaigns
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| creator_id | BIGINT | FK → users(id), NOT NULL |
| title | VARCHAR(300) | NOT NULL |
| description | TEXT | NOT NULL |
| goal_amount | DECIMAL(12,2) | NOT NULL |
| current_amount | DECIMAL(12,2) | NOT NULL DEFAULT 0 |
| start_date | DATE | NOT NULL |
| end_date | DATE | NOT NULL |
| status | VARCHAR(20) | NOT NULL DEFAULT 'DRAFT' |
| created_at | TIMESTAMP | NOT NULL DEFAULT NOW() |

### reward_tiers
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| campaign_id | BIGINT | FK → campaigns(id), NOT NULL |
| title | VARCHAR(200) | NOT NULL |
| description | TEXT | |
| min_amount | DECIMAL(10,2) | NOT NULL |
| max_backers | INT | (NULL = unlimited) |
| estimated_delivery | DATE | |

### pledges (backing records)
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| user_id | BIGINT | FK → users(id), NOT NULL |
| campaign_id | BIGINT | FK → campaigns(id), NOT NULL |
| reward_tier_id | BIGINT | FK → reward_tiers(id) |
| amount | DECIMAL(10,2) | NOT NULL |
| status | VARCHAR(20) | NOT NULL DEFAULT 'ACTIVE' |
| pledged_at | TIMESTAMP | NOT NULL DEFAULT NOW() |

## Relationships
- users → campaigns: **1:N** (creator)
- campaigns → reward_tiers: **1:N**
- users ↔ campaigns: **M:N** via `pledges` (backer relationship)
- pledges → reward_tiers: **N:1** (optional)

## ER Diagram (Text)
```
[users] 1───N [campaigns] 1───N [reward_tiers]
   │               │                  │
   └───M [pledges] M──────────────────┘
```

## Example & Insight
Kickstarter model. `current_amount` is denormalized — it equals `SUM(pledges.amount)` but avoids an expensive aggregation on every page view. Update it atomically: `UPDATE campaigns SET current_amount = current_amount + :amount WHERE id = :id`. For `max_backers` limits, use `SELECT COUNT(*) FROM pledges WHERE reward_tier_id = ? FOR UPDATE` before inserting.

## Trade-off
Denormalized `current_amount` can drift if a pledge is refunded and the update fails. Options: (1) periodic reconciliation job, (2) use a database trigger, or (3) wrap pledge + update in a single transaction. Option 3 is simplest and most reliable for moderate traffic.
