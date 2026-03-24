**30.** Equipment Rental: Customers, Equipment, Rentals, Categories. A customer can rent multiple equipment items. An equipment can be rented multiple times. An equipment can belong to multiple categories.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### customers
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(100) | NOT NULL |
| email | VARCHAR(255) | NOT NULL, UNIQUE |
| phone | VARCHAR(20) | |
| id_document | VARCHAR(50) | NOT NULL |

### equipment
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| serial_number | VARCHAR(100) | UNIQUE |
| daily_rate | DECIMAL(10,2) | NOT NULL |
| status | VARCHAR(20) | NOT NULL DEFAULT 'AVAILABLE' |
| condition | VARCHAR(20) | NOT NULL DEFAULT 'GOOD' |

### categories
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(100) | NOT NULL, UNIQUE |
| description | TEXT | |

### equipment_categories (M:N — equipment ↔ categories)
| Column | Type | Constraints |
|--------|------|-------------|
| equipment_id | BIGINT | FK → equipment(id), NOT NULL |
| category_id | BIGINT | FK → categories(id), NOT NULL |
| PK(equipment_id, category_id) | | |

### rentals
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| customer_id | BIGINT | FK → customers(id), NOT NULL |
| equipment_id | BIGINT | FK → equipment(id), NOT NULL |
| start_date | DATE | NOT NULL |
| expected_return | DATE | NOT NULL |
| actual_return | DATE | |
| total_cost | DECIMAL(10,2) | |
| status | VARCHAR(20) | NOT NULL DEFAULT 'ACTIVE' |

## Relationships
- customers → rentals: **1:N**
- equipment → rentals: **1:N**
- equipment ↔ categories: **M:N** via `equipment_categories`

## ER Diagram (Text)
```
[customers] 1───N [rentals] N───1 [equipment] 1───M [equipment_categories] M───1 [categories]
```

## Example & Insight
Key query: "Is this equipment available between date X and Y?" — `SELECT e.* FROM equipment e WHERE e.id = ? AND NOT EXISTS (SELECT 1 FROM rentals r WHERE r.equipment_id = e.id AND r.status = 'ACTIVE' AND r.start_date <= :endDate AND r.expected_return >= :startDate)`. Index on `(equipment_id, status, start_date, expected_return)` is essential.

## Trade-off
Storing `status` on equipment (AVAILABLE/RENTED/MAINTENANCE) is denormalized — you could derive it from rentals. But the denormalized flag makes availability checks O(1) instead of scanning rental history. Just ensure you update it transactionally with rental inserts/returns.
