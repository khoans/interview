**8.** Design a database for a hotel booking system. A hotel has multiple rooms. A customer can make multiple bookings. A booking can include multiple rooms. Describe the tables.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### hotel
| Column | Type | Constraints |
|--------|------|-------------|
| hotel_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| address | TEXT | NOT NULL |
| city | VARCHAR(100) | NOT NULL |
| star_rating | SMALLINT | CHECK (star_rating BETWEEN 1 AND 5) |

### room
| Column | Type | Constraints |
|--------|------|-------------|
| room_id | BIGINT | PK, AUTO_INCREMENT |
| hotel_id | BIGINT | FK → hotel(hotel_id), NOT NULL |
| room_number | VARCHAR(10) | NOT NULL |
| room_type | VARCHAR(50) | NOT NULL |
| price_per_night | DECIMAL(10,2) | NOT NULL |
| UNIQUE | | (hotel_id, room_number) |

### customer
| Column | Type | Constraints |
|--------|------|-------------|
| customer_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| email | VARCHAR(255) | UNIQUE, NOT NULL |
| phone | VARCHAR(20) | NULL |
| id_document | VARCHAR(50) | NULL |

### booking
| Column | Type | Constraints |
|--------|------|-------------|
| booking_id | BIGINT | PK, AUTO_INCREMENT |
| customer_id | BIGINT | FK → customer(customer_id), NOT NULL |
| check_in | DATE | NOT NULL |
| check_out | DATE | NOT NULL |
| status | VARCHAR(20) | NOT NULL, DEFAULT 'CONFIRMED' |
| total_amount | DECIMAL(12,2) | NOT NULL |
| booked_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP |

### booking_room (junction — Booking ↔ Room)
| Column | Type | Constraints |
|--------|------|-------------|
| booking_id | BIGINT | PK, FK → booking(booking_id) |
| room_id | BIGINT | PK, FK → room(room_id) |
| price_per_night | DECIMAL(10,2) | NOT NULL |

## Relationships
- **Hotel → Room**: 1:N
- **Customer → Booking**: 1:N
- **Booking ↔ Room**: M:N via `booking_room`

## ER Diagram
```
+--------+       +--------+       +--------------+       +--------+
| hotel  |       |  room  |       | booking_room |       | booking|
+--------+       +--------+       +--------------+       +--------+
|hotel_id|──1:N──|room_id |──1:N──|room_id   (PK)|──N:1──|booking_id|
| name   |       |hotel_id|       |booking_id(PK)|       |customer_id|──N:1──customer
| address|       |room_no |       |price_per_nite|       |check_in  |
| city   |       |room_type|      +--------------+       |check_out |
|star_rtg|       |price   |                              |status    |
+--------+       +--------+                              |total_amt |
                                                         +----------+
```

## Key Design Insights
- **Availability check is the hardest query**: To find available rooms for a date range, you need `WHERE room_id NOT IN (SELECT room_id FROM booking_room br JOIN booking b ON br.booking_id = b.booking_id WHERE b.check_in < :checkout AND b.check_out > :checkin AND b.status != 'CANCELLED')`. Index on `booking(check_in, check_out, status)` is critical.
- **UNIQUE(hotel_id, room_number)** ensures room numbers are unique within a hotel but can repeat across hotels (Room 101 in Hotel A and Hotel B).
- **Trade-off**: Snapshot `price_per_night` in `booking_room` because room rates change seasonally. Without this, you'd lose historical pricing data. The `total_amount` in booking is denormalized for fast retrieval but should equal `SUM(price_per_night * nights)` across all rooms.
