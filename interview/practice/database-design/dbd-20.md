**20.** Design a database for a conference management system. A conference can have multiple sessions. A session can have multiple speakers. An attendee can register for multiple sessions. Describe the tables.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### conference
| Column | Type | Constraints |
|--------|------|-------------|
| conference_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(300) | NOT NULL |
| location | VARCHAR(200) | NOT NULL |
| start_date | DATE | NOT NULL |
| end_date | DATE | NOT NULL |
| description | TEXT | NULL |

### session
| Column | Type | Constraints |
|--------|------|-------------|
| session_id | BIGINT | PK, AUTO_INCREMENT |
| conference_id | BIGINT | FK → conference(conference_id), NOT NULL |
| title | VARCHAR(300) | NOT NULL |
| room | VARCHAR(100) | NULL |
| start_time | TIMESTAMP | NOT NULL |
| end_time | TIMESTAMP | NOT NULL |
| max_capacity | INT | NULL |

### speaker
| Column | Type | Constraints |
|--------|------|-------------|
| speaker_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| email | VARCHAR(255) | UNIQUE, NOT NULL |
| bio | TEXT | NULL |
| company | VARCHAR(200) | NULL |

### session_speaker (junction — Session ↔ Speaker)
| Column | Type | Constraints |
|--------|------|-------------|
| session_id | BIGINT | PK, FK → session(session_id) |
| speaker_id | BIGINT | PK, FK → speaker(speaker_id) |
| role | VARCHAR(50) | NOT NULL, DEFAULT 'SPEAKER' |

### attendee
| Column | Type | Constraints |
|--------|------|-------------|
| attendee_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| email | VARCHAR(255) | UNIQUE, NOT NULL |
| company | VARCHAR(200) | NULL |

### session_registration (junction — Session ↔ Attendee)
| Column | Type | Constraints |
|--------|------|-------------|
| session_id | BIGINT | PK, FK → session(session_id) |
| attendee_id | BIGINT | PK, FK → attendee(attendee_id) |
| registered_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP |

## Relationships
- **Conference → Session**: 1:N
- **Session ↔ Speaker**: M:N via `session_speaker`
- **Session ↔ Attendee**: M:N via `session_registration`

## ER Diagram
```
+------------+       +----------+       +-----------------+       +---------+
| conference |       |  session |       | session_speaker |       | speaker |
+------------+       +----------+       +-----------------+       +---------+
|conference_id|─1:N──|session_id|──1:N──|session_id  (PK) |──N:1──|speaker_id|
| name       |       |conf_id  |       |speaker_id  (PK) |       | name    |
| location   |       | title   |       | role            |       | email   |
| start_date |       | room    |       +-----------------+       | bio     |
| end_date   |       |start_time|                                | company |
+------------+       |end_time  |      +----------------------+  +---------+
                     |max_cap   |      | session_registration |
                     +-----+----+      +----------------------+
                           |──────1:N──|session_id   (PK)     |
                                       |attendee_id  (PK)     |──N:1──attendee
                                       |registered_at         |
                                       +----------------------+
```

## Key Design Insights
- **Two M:N relationships on `session`** — speakers and attendees. This is a common pattern where one entity (session) acts as a hub. The `role` field on `session_speaker` distinguishes keynote speakers from panelists from moderators — important for conference scheduling.
- **Schedule conflict detection** is the key query: `SELECT * FROM session_registration sr JOIN session s ON sr.session_id = s.session_id WHERE sr.attendee_id = :id AND s.start_time < :end AND s.end_time > :start`. This prevents an attendee from registering for overlapping sessions.
- **Trade-off**: `speaker` and `attendee` are separate tables, but in reality a speaker at one session is often an attendee at others. A unified `person` table with role-based junction tables is cleaner for production. For the interview, separate tables match the question's domain language — mention the unification as an optimization.
