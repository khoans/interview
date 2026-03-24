**14.** Design a database for a real estate agency. An agent can list multiple properties. A property can have multiple viewings. A buyer can view multiple properties. Describe the tables.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### agent
| Column | Type | Constraints |
|--------|------|-------------|
| agent_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| email | VARCHAR(255) | UNIQUE, NOT NULL |
| phone | VARCHAR(20) | NOT NULL |
| license_number | VARCHAR(50) | UNIQUE, NOT NULL |

### property
| Column | Type | Constraints |
|--------|------|-------------|
| property_id | BIGINT | PK, AUTO_INCREMENT |
| agent_id | BIGINT | FK → agent(agent_id), NOT NULL |
| address | TEXT | NOT NULL |
| city | VARCHAR(100) | NOT NULL |
| property_type | VARCHAR(50) | NOT NULL |
| price | DECIMAL(15,2) | NOT NULL |
| bedrooms | INT | NULL |
| area_sqft | DECIMAL(10,2) | NULL |
| status | VARCHAR(20) | NOT NULL, DEFAULT 'AVAILABLE' |
| listed_at | DATE | NOT NULL |

### buyer
| Column | Type | Constraints |
|--------|------|-------------|
| buyer_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| email | VARCHAR(255) | UNIQUE, NOT NULL |
| phone | VARCHAR(20) | NULL |
| budget_max | DECIMAL(15,2) | NULL |

### viewing
| Column | Type | Constraints |
|--------|------|-------------|
| viewing_id | BIGINT | PK, AUTO_INCREMENT |
| property_id | BIGINT | FK → property(property_id), NOT NULL |
| buyer_id | BIGINT | FK → buyer(buyer_id), NOT NULL |
| viewing_date | TIMESTAMP | NOT NULL |
| feedback | TEXT | NULL |
| interest_level | VARCHAR(20) | NULL |

## Relationships
- **Agent → Property**: 1:N (an agent lists many properties)
- **Property → Viewing**: 1:N (a property can have many viewings)
- **Buyer → Viewing**: 1:N (a buyer can attend many viewings)
- **Buyer ↔ Property**: effectively M:N via `viewing`

## ER Diagram
```
+--------+       +----------+       +----------+       +--------+
| agent  |       | property |       |  viewing |       | buyer  |
+--------+       +----------+       +----------+       +--------+
|agent_id|──1:N──|property_id|─1:N──|viewing_id|──N:1──|buyer_id|
| name   |       | agent_id |       |property_id|      | name   |
| email  |       | address  |       | buyer_id |       | email  |
| phone  |       | city     |       |viewing_dt|       | phone  |
|license |       |prop_type |       | feedback |       |budget  |
+--------+       | price    |       |interest  |       +--------+
                 | bedrooms |       +----------+
                 | area     |
                 | status   |
                 | listed_at|
                 +----------+
```

## Key Design Insights
- **`viewing` is a transactional junction** resolving the Buyer ↔ Property M:N relationship. Unlike a pure junction table, it has its own surrogate PK because the same buyer might view the same property multiple times (second viewing, open house events, etc.).
- **`interest_level`** on viewing captures CRM-valuable data — the agent can query "which buyers showed HIGH interest in properties over $500K" for targeted follow-ups. Index on `(property_id, interest_level)` supports this.
- **Trade-off**: `property.status` is denormalized (AVAILABLE/UNDER_OFFER/SOLD). It could be derived from viewing/offer records, but agents need to quickly filter active listings. In production, you'd add an `offer` table linking buyer→property with offer amount and status, but for the core question this design is sufficient.
