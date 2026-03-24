**5.** Design a database for a movie theater. A movie can have multiple showtimes. A showtime can have multiple ticket bookings. A customer can book multiple tickets. Describe the tables.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### movie
| Column | Type | Constraints |
|--------|------|-------------|
| movie_id | BIGINT | PK, AUTO_INCREMENT |
| title | VARCHAR(300) | NOT NULL |
| duration_min | INT | NOT NULL |
| genre | VARCHAR(100) | NULL |
| rating | VARCHAR(10) | NULL |
| release_date | DATE | NULL |

### screen
| Column | Type | Constraints |
|--------|------|-------------|
| screen_id | BIGINT | PK, AUTO_INCREMENT |
| screen_name | VARCHAR(50) | NOT NULL |
| total_seats | INT | NOT NULL |

### showtime
| Column | Type | Constraints |
|--------|------|-------------|
| showtime_id | BIGINT | PK, AUTO_INCREMENT |
| movie_id | BIGINT | FK → movie(movie_id), NOT NULL |
| screen_id | BIGINT | FK → screen(screen_id), NOT NULL |
| start_time | TIMESTAMP | NOT NULL |
| price | DECIMAL(8,2) | NOT NULL |

### customer
| Column | Type | Constraints |
|--------|------|-------------|
| customer_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| email | VARCHAR(255) | UNIQUE, NOT NULL |
| phone | VARCHAR(20) | NULL |

### ticket
| Column | Type | Constraints |
|--------|------|-------------|
| ticket_id | BIGINT | PK, AUTO_INCREMENT |
| showtime_id | BIGINT | FK → showtime(showtime_id), NOT NULL |
| customer_id | BIGINT | FK → customer(customer_id), NOT NULL |
| seat_number | VARCHAR(10) | NOT NULL |
| booked_at | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP |
| UNIQUE | | (showtime_id, seat_number) |

## Relationships
- **Movie → Showtime**: 1:N
- **Screen → Showtime**: 1:N
- **Customer → Ticket**: 1:N
- **Showtime → Ticket**: 1:N

## ER Diagram
```
+--------+       +-----------+       +----------+
| movie  |       | showtime  |       |  screen  |
+--------+       +-----------+       +----------+
|movie_id |─1:N─|showtime_id|──N:1──|screen_id  |
| title  |      | movie_id  |       |screen_name|
|duration|      | screen_id |       |total_seats|
| genre  |      | start_time|       +----------+
| rating |      | price     |
+--------+      +-----+-----+
                      |1:N
                +-----+-----+
                |   ticket   |
                +------------+
                | ticket_id  |
                | showtime_id|
                | customer_id|──N:1──customer
                | seat_number|
                | booked_at  |
                +------------+
```

## Key Design Insights
- **UNIQUE constraint on `(showtime_id, seat_number)`** prevents double-booking the same seat for the same showtime. This is the critical business rule enforced at the database level, not just application logic. In high-concurrency scenarios, you'd also use `SELECT ... FOR UPDATE` to handle race conditions.
- **Separate `screen` table** is needed because a movie theater has physical rooms — showtimes need to know which screen/room, and total_seats determines capacity. Without it, you can't validate seat availability.
- **Trade-off**: `price` lives on `showtime` (not `movie`) because pricing depends on time slot (matinee vs. evening), screen type (IMAX vs. regular), etc. You could further normalize with a pricing rules table, but for most theaters, per-showtime pricing is the pragmatic sweet spot.
