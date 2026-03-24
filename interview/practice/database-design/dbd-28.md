**28.** Event Management: Events, Sponsors, Attendees. An event can have multiple sponsors. A sponsor can sponsor multiple events. An attendee can attend multiple events.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### events
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| description | TEXT | |
| venue | VARCHAR(200) | |
| event_date | TIMESTAMP | NOT NULL |
| max_capacity | INT | |
| status | VARCHAR(20) | NOT NULL DEFAULT 'UPCOMING' |

### sponsors
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| company_name | VARCHAR(200) | NOT NULL |
| contact_email | VARCHAR(255) | NOT NULL |
| website | VARCHAR(500) | |

### attendees
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(100) | NOT NULL |
| email | VARCHAR(255) | NOT NULL, UNIQUE |
| phone | VARCHAR(20) | |

### event_sponsors (M:N — events ↔ sponsors)
| Column | Type | Constraints |
|--------|------|-------------|
| event_id | BIGINT | FK → events(id), NOT NULL |
| sponsor_id | BIGINT | FK → sponsors(id), NOT NULL |
| tier | VARCHAR(20) | NOT NULL (PLATINUM, GOLD, SILVER, BRONZE) |
| amount | DECIMAL(12,2) | NOT NULL |
| PK(event_id, sponsor_id) | | |

### event_attendees (M:N — events ↔ attendees)
| Column | Type | Constraints |
|--------|------|-------------|
| event_id | BIGINT | FK → events(id), NOT NULL |
| attendee_id | BIGINT | FK → attendees(id), NOT NULL |
| registered_at | TIMESTAMP | NOT NULL DEFAULT NOW() |
| checked_in | BOOLEAN | DEFAULT FALSE |
| PK(event_id, attendee_id) | | |

## Relationships
- events ↔ sponsors: **M:N** via `event_sponsors`
- events ↔ attendees: **M:N** via `event_attendees`

## ER Diagram (Text)
```
[sponsors] 1───M [event_sponsors] M───1 [events] 1───M [event_attendees] M───1 [attendees]
```

## Example & Insight
The `tier` and `amount` on the junction table is key — sponsorship level belongs to the *relationship*, not the sponsor entity. A company might be Platinum for one event and Silver for another. Index `(event_id, checked_in)` to quickly count check-ins vs registrations at event time.

## Trade-off
Storing `max_capacity` on the event means you need an application-level check before inserting into `event_attendees`. For high concurrency (ticket sales), use `SELECT ... FOR UPDATE` or an optimistic lock to prevent overselling.
