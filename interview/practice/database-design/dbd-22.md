**22.** Design a database for a pet adoption center. A pet can have multiple vaccinations. A volunteer can care for multiple pets. An adopter can adopt multiple pets. Describe the tables.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### pet
| Column | Type | Constraints |
|--------|------|-------------|
| pet_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(100) | NOT NULL |
| species | VARCHAR(50) | NOT NULL |
| breed | VARCHAR(100) | NULL |
| age_months | INT | NULL |
| gender | CHAR(1) | CHECK (gender IN ('M','F')) |
| intake_date | DATE | NOT NULL |
| status | VARCHAR(20) | NOT NULL, DEFAULT 'AVAILABLE' |

### vaccination
| Column | Type | Constraints |
|--------|------|-------------|
| vaccination_id | BIGINT | PK, AUTO_INCREMENT |
| pet_id | BIGINT | FK → pet(pet_id), NOT NULL |
| vaccine_name | VARCHAR(200) | NOT NULL |
| administered_date | DATE | NOT NULL |
| next_due_date | DATE | NULL |
| veterinarian | VARCHAR(200) | NULL |

### volunteer
| Column | Type | Constraints |
|--------|------|-------------|
| volunteer_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| email | VARCHAR(255) | UNIQUE, NOT NULL |
| phone | VARCHAR(20) | NULL |
| availability | VARCHAR(100) | NULL |

### pet_care (junction — Volunteer ↔ Pet)
| Column | Type | Constraints |
|--------|------|-------------|
| volunteer_id | BIGINT | PK, FK → volunteer(volunteer_id) |
| pet_id | BIGINT | PK, FK → pet(pet_id) |
| care_type | VARCHAR(50) | NOT NULL |
| start_date | DATE | NOT NULL |

### adopter
| Column | Type | Constraints |
|--------|------|-------------|
| adopter_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| email | VARCHAR(255) | UNIQUE, NOT NULL |
| phone | VARCHAR(20) | NOT NULL |
| address | TEXT | NOT NULL |

### adoption
| Column | Type | Constraints |
|--------|------|-------------|
| adoption_id | BIGINT | PK, AUTO_INCREMENT |
| pet_id | BIGINT | FK → pet(pet_id), NOT NULL |
| adopter_id | BIGINT | FK → adopter(adopter_id), NOT NULL |
| adoption_date | DATE | NOT NULL |
| fee | DECIMAL(8,2) | NOT NULL, DEFAULT 0.00 |
| notes | TEXT | NULL |

## Relationships
- **Pet → Vaccination**: 1:N
- **Volunteer ↔ Pet**: M:N via `pet_care`
- **Adopter → Adoption**: 1:N
- **Pet → Adoption**: 1:N (a pet could be returned and re-adopted)

## ER Diagram
```
+-----------+       +--------+       +--------------+       +-----------+
| volunteer |       |pet_care|       |     pet      |       |vaccination|
+-----------+       +--------+       +--------------+       +-----------+
|volunteer_id|─1:N──|vol_id(PK)|─N:1─| pet_id      |──1:N──|vacc_id   |
| name      |       |pet_id(PK)|     | name        |       | pet_id   |
| email     |       |care_type |     | species     |       |vaccine_nm|
| phone     |       |start_date|     | breed       |       |admin_date|
|availability|      +--------+       | age_months  |       |next_due  |
+-----------+                        | gender      |       |vet       |
                                     | intake_date |       +-----------+
                                     | status      |
                    +-----------+    +------+-------+
                    |  adopter  |           |
                    +-----------+     +-----+------+
                    |adopter_id |     |  adoption  |
                    | name      |─1:N─|adoption_id |
                    | email     |     | pet_id     |
                    | phone     |     | adopter_id |
                    | address   |     |adoption_dt |
                    +-----------+     | fee        |
                                      | notes      |
                                      +------------+
```

## Key Design Insights
- **`adoption` uses a surrogate PK** (not composite) because a pet could theoretically be returned and re-adopted. If you used `(pet_id, adopter_id)` as PK, the same adopter couldn't adopt the same pet again after a return. The `pet.status` field tracks current state: AVAILABLE, ADOPTED, MEDICAL_HOLD.
- **`vaccination.next_due_date`** enables proactive care alerts: `WHERE next_due_date <= CURRENT_DATE + INTERVAL '7 days' AND pet.status = 'AVAILABLE'` — find pets needing vaccinations soon. This is practical shelter management.
- **Trade-off**: `pet_care` is modeled as M:N (a volunteer cares for multiple pets, a pet can be cared for by multiple volunteers). In practice, you might want a log-style table with timestamps for each care event (feeding, walking, grooming) rather than a static assignment. The junction table works for assignment; a `care_log` table with `(volunteer_id, pet_id, activity, timestamp)` works for tracking.
