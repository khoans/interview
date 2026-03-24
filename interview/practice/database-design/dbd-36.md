**36.** Healthcare Appointment: Patients, Doctors, Appointments, Follow-ups. A patient can book multiple appointments. A doctor can have multiple appointments. An appointment can have multiple follow-ups.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### patients
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(100) | NOT NULL |
| date_of_birth | DATE | NOT NULL |
| phone | VARCHAR(20) | NOT NULL |
| email | VARCHAR(255) | |
| blood_type | VARCHAR(5) | |
| insurance_id | VARCHAR(50) | |

### doctors
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(100) | NOT NULL |
| specialization | VARCHAR(100) | NOT NULL |
| license_number | VARCHAR(50) | NOT NULL, UNIQUE |
| phone | VARCHAR(20) | NOT NULL |

### appointments
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| patient_id | BIGINT | FK → patients(id), NOT NULL |
| doctor_id | BIGINT | FK → doctors(id), NOT NULL |
| appointment_date | TIMESTAMP | NOT NULL |
| duration_min | INT | NOT NULL DEFAULT 30 |
| reason | TEXT | |
| diagnosis | TEXT | |
| status | VARCHAR(20) | NOT NULL DEFAULT 'SCHEDULED' |
| created_at | TIMESTAMP | NOT NULL DEFAULT NOW() |

### follow_ups
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| appointment_id | BIGINT | FK → appointments(id), NOT NULL |
| scheduled_date | TIMESTAMP | NOT NULL |
| notes | TEXT | |
| status | VARCHAR(20) | NOT NULL DEFAULT 'PENDING' |
| completed_at | TIMESTAMP | |

## Relationships
- patients → appointments: **1:N**
- doctors → appointments: **1:N**
- appointments → follow_ups: **1:N**

## ER Diagram (Text)
```
[patients] 1───N [appointments] N───1 [doctors]
                      │
                     1:N
                      │
                 [follow_ups]
```

## Example & Insight
Preventing double-booking is the core challenge. Add a unique constraint or use an exclusion constraint: no two appointments for the same doctor can overlap in time. In PostgreSQL: `EXCLUDE USING gist (doctor_id WITH =, tsrange(appointment_date, appointment_date + duration_min * interval '1 minute') WITH &&)`. Simpler approach: index `(doctor_id, appointment_date)` and check in application code.

## Trade-off
Follow-ups as a separate table (vs. self-referencing appointments) is cleaner because follow-ups have different fields (no doctor selection, no diagnosis yet). But if a follow-up becomes a full appointment (new diagnosis, different doctor), you need to "promote" it — either copy to appointments table or make follow-ups a subtype of appointments.
