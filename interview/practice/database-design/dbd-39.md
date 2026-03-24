**39.** Parking Management: Parking Lots, Spots, Reservations, Time Slots. A parking lot can have multiple parking spots. A customer can make multiple reservations. A reservation can cover multiple time slots.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### customers
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(100) | NOT NULL |
| phone | VARCHAR(20) | NOT NULL, UNIQUE |
| vehicle_plate | VARCHAR(20) | NOT NULL |

### parking_lots
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| address | VARCHAR(300) | NOT NULL |
| total_spots | INT | NOT NULL |
| hourly_rate | DECIMAL(8,2) | NOT NULL |

### parking_spots
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| lot_id | BIGINT | FK → parking_lots(id), NOT NULL |
| spot_number | VARCHAR(10) | NOT NULL |
| spot_type | VARCHAR(20) | NOT NULL DEFAULT 'REGULAR' |
| is_active | BOOLEAN | NOT NULL DEFAULT TRUE |
| UNIQUE(lot_id, spot_number) | | |

### time_slots
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| slot_date | DATE | NOT NULL |
| start_time | TIME | NOT NULL |
| end_time | TIME | NOT NULL |
| UNIQUE(slot_date, start_time, end_time) | | |

### reservations
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| customer_id | BIGINT | FK → customers(id), NOT NULL |
| spot_id | BIGINT | FK → parking_spots(id), NOT NULL |
| total_cost | DECIMAL(10,2) | NOT NULL |
| status | VARCHAR(20) | NOT NULL DEFAULT 'CONFIRMED' |
| created_at | TIMESTAMP | NOT NULL DEFAULT NOW() |

### reservation_time_slots (M:N — reservations ↔ time_slots)
| Column | Type | Constraints |
|--------|------|-------------|
| reservation_id | BIGINT | FK → reservations(id), NOT NULL |
| time_slot_id | BIGINT | FK → time_slots(id), NOT NULL |
| PK(reservation_id, time_slot_id) | | |

## Relationships
- parking_lots → parking_spots: **1:N**
- customers → reservations: **1:N**
- reservations → parking_spots: **N:1**
- reservations ↔ time_slots: **M:N** via `reservation_time_slots`

## ER Diagram (Text)
```
[customers] 1───N [reservations] N───1 [parking_spots] N───1 [parking_lots]
                        │
                       M:N
                        │
            [reservation_time_slots]
                        │
                       M:1
                        │
                   [time_slots]
```

## Example & Insight
To find available spots: `SELECT ps.* FROM parking_spots ps WHERE ps.lot_id = :lotId AND ps.id NOT IN (SELECT r.spot_id FROM reservations r JOIN reservation_time_slots rts ON r.id = rts.reservation_id WHERE rts.time_slot_id IN (:requestedSlotIds) AND r.status = 'CONFIRMED')`. Pre-generating time_slots (e.g., hourly blocks for the next 30 days) simplifies availability queries.

## Trade-off
Pre-generated time slots create many rows but make availability checks simple set operations. Alternative: store `start_time`/`end_time` directly on reservations and use range overlap queries. The range approach is more flexible but overlap detection is harder to get right and index efficiently.
