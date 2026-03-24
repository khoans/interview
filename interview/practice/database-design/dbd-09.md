**9.** Design a database for a car rental company. A customer can rent multiple cars. A car can be rented multiple times. A car can have multiple maintenance records. Describe the tables.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### customer
| Column | Type | Constraints |
|--------|------|-------------|
| customer_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| email | VARCHAR(255) | UNIQUE, NOT NULL |
| phone | VARCHAR(20) | NULL |
| driver_license | VARCHAR(50) | UNIQUE, NOT NULL |

### car
| Column | Type | Constraints |
|--------|------|-------------|
| car_id | BIGINT | PK, AUTO_INCREMENT |
| license_plate | VARCHAR(20) | UNIQUE, NOT NULL |
| make | VARCHAR(100) | NOT NULL |
| model | VARCHAR(100) | NOT NULL |
| year | SMALLINT | NOT NULL |
| daily_rate | DECIMAL(8,2) | NOT NULL |
| status | VARCHAR(20) | NOT NULL, DEFAULT 'AVAILABLE' |

### rental
| Column | Type | Constraints |
|--------|------|-------------|
| rental_id | BIGINT | PK, AUTO_INCREMENT |
| customer_id | BIGINT | FK → customer(customer_id), NOT NULL |
| car_id | BIGINT | FK → car(car_id), NOT NULL |
| pickup_date | DATE | NOT NULL |
| return_date | DATE | NULL |
| expected_return | DATE | NOT NULL |
| total_cost | DECIMAL(10,2) | NULL |
| status | VARCHAR(20) | NOT NULL, DEFAULT 'ACTIVE' |

### maintenance
| Column | Type | Constraints |
|--------|------|-------------|
| maintenance_id | BIGINT | PK, AUTO_INCREMENT |
| car_id | BIGINT | FK → car(car_id), NOT NULL |
| maintenance_date | DATE | NOT NULL |
| description | TEXT | NOT NULL |
| cost | DECIMAL(10,2) | NOT NULL |
| next_due_date | DATE | NULL |

## Relationships
- **Customer → Rental**: 1:N
- **Car → Rental**: 1:N (same car rented over time — this resolves the M:N between Customer and Car)
- **Car → Maintenance**: 1:N

## ER Diagram
```
+----------+       +---------+       +-------+
| customer |       |  rental |       |  car  |
+----------+       +---------+       +-------+
|customer_id|─1:N──|rental_id|──N:1──|car_id |
| name     |       |customer_id|     |plate  |
| email    |       |car_id    |      |make   |
|phone     |       |pickup_dt |      |model  |
|license   |       |return_dt |      |year   |
+----------+       |expected  |      |daily_rt|
                   |total_cost|      |status  |
                   |status    |      +---+---+
                   +---------+          |1:N
                                  +-----+-------+
                                  | maintenance |
                                  +-------------+
                                  |maint_id     |
                                  | car_id      |
                                  | maint_date  |
                                  | description |
                                  | cost        |
                                  |next_due_date|
                                  +-------------+
```

## Key Design Insights
- **Customer ↔ Car is logically M:N** but resolved through `rental` — which is a transactional table, not a pure junction. Each rental is a time-bound event, so the same customer can rent the same car multiple times (different rows, different dates). Using `rental_id` as PK (not composite) reflects this.
- **`car.status`** is a denormalized field updated by the application when rentals start/end or maintenance begins. Alternatively, you could derive it from rental/maintenance records, but a status field avoids complex queries for "show me all available cars right now."
- **Trade-off**: `return_date` being NULL means the car is currently rented. `expected_return` enables overdue detection: `WHERE return_date IS NULL AND expected_return < CURRENT_DATE`. Index on `(car_id, return_date)` supports both availability and history queries.
