**16.** Design a database for a flight booking system. An airport can have multiple flights. A flight can have multiple passengers. A passenger can book multiple flights. Describe the tables.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### airport
| Column | Type | Constraints |
|--------|------|-------------|
| airport_id | BIGINT | PK, AUTO_INCREMENT |
| code | CHAR(3) | UNIQUE, NOT NULL |
| name | VARCHAR(200) | NOT NULL |
| city | VARCHAR(100) | NOT NULL |
| country | VARCHAR(100) | NOT NULL |

### flight
| Column | Type | Constraints |
|--------|------|-------------|
| flight_id | BIGINT | PK, AUTO_INCREMENT |
| flight_number | VARCHAR(10) | NOT NULL |
| departure_airport_id | BIGINT | FK → airport(airport_id), NOT NULL |
| arrival_airport_id | BIGINT | FK → airport(airport_id), NOT NULL |
| departure_time | TIMESTAMP | NOT NULL |
| arrival_time | TIMESTAMP | NOT NULL |
| total_seats | INT | NOT NULL |
| price | DECIMAL(10,2) | NOT NULL |
| status | VARCHAR(20) | NOT NULL, DEFAULT 'SCHEDULED' |

### passenger
| Column | Type | Constraints |
|--------|------|-------------|
| passenger_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| email | VARCHAR(255) | UNIQUE, NOT NULL |
| phone | VARCHAR(20) | NULL |
| passport_number | VARCHAR(20) | UNIQUE, NOT NULL |

### booking
| Column | Type | Constraints |
|--------|------|-------------|
| booking_id | BIGINT | PK, AUTO_INCREMENT |
| passenger_id | BIGINT | FK → passenger(passenger_id), NOT NULL |
| flight_id | BIGINT | FK → flight(flight_id), NOT NULL |
| seat_number | VARCHAR(5) | NULL |
| booking_date | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP |
| status | VARCHAR(20) | NOT NULL, DEFAULT 'CONFIRMED' |
| price_paid | DECIMAL(10,2) | NOT NULL |

## Relationships
- **Airport → Flight**: 1:N (twice — as departure and arrival)
- **Passenger ↔ Flight**: M:N via `booking`
- **Passenger → Booking**: 1:N
- **Flight → Booking**: 1:N

## ER Diagram
```
+---------+              +---------+
| airport |              | airport |
+---------+              +---------+
|airport_id|──(depart)   |airport_id|──(arrive)
| code    |     \        | code    |    /
| name    |      \       | name    |   /
| city    |    1:N\      | city    | /1:N
| country |       +------+--------+
+---------+       |    flight     |
                  +---------------+
                  | flight_id     |
                  | flight_number |
                  |depart_airport |
                  |arrive_airport |
                  | depart_time   |
                  | arrive_time   |
                  | total_seats   |
                  | price         |
                  | status        |
                  +-------+-------+
                          |1:N
                  +-------+-------+
                  |    booking    |
                  +---------------+
                  | booking_id    |
                  | passenger_id  |──N:1──passenger
                  | flight_id     |
                  | seat_number   |
                  | booking_date  |
                  | status        |
                  | price_paid    |
                  +---------------+
```

## Key Design Insights
- **Two FKs to the same table** — `flight` references `airport` twice (departure and arrival). This is a common pattern but requires two separate JOIN aliases in queries: `JOIN airport dep ON f.departure_airport_id = dep.airport_id JOIN airport arr ON f.arrival_airport_id = arr.airport_id`. Interviewers specifically look for this.
- **`booking` resolves Passenger ↔ Flight M:N** with a surrogate PK because a passenger could theoretically rebook the same flight (after cancellation). `price_paid` is a snapshot — flight prices change dynamically.
- **Trade-off**: `flight.total_seats` minus `COUNT(booking WHERE status='CONFIRMED')` gives available seats. In high-traffic systems (like airline booking), this naive approach causes race conditions. Production systems use a `seats_available` counter with optimistic locking (`UPDATE flight SET seats_available = seats_available - 1 WHERE seats_available > 0`) or a seat inventory table.
