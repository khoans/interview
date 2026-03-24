**33.** Ride-Sharing: Drivers, Passengers, Rides, Stops. A driver can give multiple rides. A passenger can take multiple rides. A ride can have multiple stops.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### drivers
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(100) | NOT NULL |
| phone | VARCHAR(20) | NOT NULL, UNIQUE |
| license_number | VARCHAR(50) | NOT NULL, UNIQUE |
| vehicle_plate | VARCHAR(20) | NOT NULL |
| rating | DECIMAL(2,1) | DEFAULT 5.0 |
| status | VARCHAR(20) | NOT NULL DEFAULT 'OFFLINE' |

### passengers
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(100) | NOT NULL |
| phone | VARCHAR(20) | NOT NULL, UNIQUE |
| email | VARCHAR(255) | UNIQUE |
| rating | DECIMAL(2,1) | DEFAULT 5.0 |

### rides
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| driver_id | BIGINT | FK → drivers(id), NOT NULL |
| passenger_id | BIGINT | FK → passengers(id), NOT NULL |
| pickup_lat | DECIMAL(9,6) | NOT NULL |
| pickup_lng | DECIMAL(9,6) | NOT NULL |
| dropoff_lat | DECIMAL(9,6) | NOT NULL |
| dropoff_lng | DECIMAL(9,6) | NOT NULL |
| fare | DECIMAL(10,2) | |
| status | VARCHAR(20) | NOT NULL DEFAULT 'REQUESTED' |
| requested_at | TIMESTAMP | NOT NULL DEFAULT NOW() |
| started_at | TIMESTAMP | |
| completed_at | TIMESTAMP | |

### ride_stops
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| ride_id | BIGINT | FK → rides(id), NOT NULL |
| stop_order | INT | NOT NULL |
| lat | DECIMAL(9,6) | NOT NULL |
| lng | DECIMAL(9,6) | NOT NULL |
| address | VARCHAR(300) | |
| arrived_at | TIMESTAMP | |
| UNIQUE(ride_id, stop_order) | | |

## Relationships
- drivers → rides: **1:N**
- passengers → rides: **1:N**
- rides → ride_stops: **1:N**

## ER Diagram (Text)
```
[drivers] 1───N [rides] N───1 [passengers]
                   │
                  1:N
                   │
              [ride_stops]
```

## Example & Insight
Uber/Grab model. `stop_order` enforces the sequence of stops. Status flow: REQUESTED → ACCEPTED → DRIVER_ARRIVING → IN_PROGRESS → COMPLETED / CANCELLED. Index `(driver_id, status)` for "find available driver" and `(passenger_id, requested_at DESC)` for ride history.

## Trade-off
Storing lat/lng as DECIMAL works but doesn't support spatial queries like "find drivers within 2km." For production, use PostGIS `GEOGRAPHY` type with spatial index — but that adds complexity. Start with DECIMAL + application-level distance calc, migrate to PostGIS when query volume demands it.
