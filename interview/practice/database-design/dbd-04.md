**4.** Design a database for a hospital. A doctor can treat multiple patients. A patient can be treated by multiple doctors. A patient can have multiple medical tests. Describe the tables.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### doctor
| Column | Type | Constraints |
|--------|------|-------------|
| doctor_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| specialization | VARCHAR(100) | NOT NULL |
| phone | VARCHAR(20) | NULL |
| license_number | VARCHAR(50) | UNIQUE, NOT NULL |

### patient
| Column | Type | Constraints |
|--------|------|-------------|
| patient_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| dob | DATE | NOT NULL |
| gender | CHAR(1) | NOT NULL, CHECK (gender IN ('M','F','O')) |
| phone | VARCHAR(20) | NULL |
| blood_type | VARCHAR(3) | NULL |

### treatment (junction — Doctor ↔ Patient)
| Column | Type | Constraints |
|--------|------|-------------|
| treatment_id | BIGINT | PK, AUTO_INCREMENT |
| doctor_id | BIGINT | FK → doctor(doctor_id), NOT NULL |
| patient_id | BIGINT | FK → patient(patient_id), NOT NULL |
| diagnosis | TEXT | NOT NULL |
| treatment_date | DATE | NOT NULL |
| notes | TEXT | NULL |

### medical_test
| Column | Type | Constraints |
|--------|------|-------------|
| test_id | BIGINT | PK, AUTO_INCREMENT |
| patient_id | BIGINT | FK → patient(patient_id), NOT NULL |
| treatment_id | BIGINT | FK → treatment(treatment_id), NULL |
| test_name | VARCHAR(200) | NOT NULL |
| test_date | TIMESTAMP | NOT NULL |
| result | TEXT | NULL |
| status | VARCHAR(20) | NOT NULL, DEFAULT 'PENDING' |

## Relationships
- **Doctor ↔ Patient**: M:N via `treatment` (a doctor treats many patients, a patient sees many doctors)
- **Patient → Medical Test**: 1:N
- **Treatment → Medical Test**: 1:N (optional link — a test may be ordered as part of a treatment)

## ER Diagram
```
+--------+       +------------+       +---------+
| doctor |       |  treatment |       | patient |
+--------+       +------------+       +---------+
|doctor_id|─1:N─|treatment_id|──N:1──|patient_id|
| name   |      | doctor_id  |       | name    |
| special|      | patient_id |       | dob     |
| phone  |      | diagnosis  |       | gender  |
|license |      | treat_date |       | phone   |
+--------+      | notes      |       |blood_type|
                +-----+------+       +----+----+
                      |                    |
                      |1:N            1:N  |
                      |    +-----------+   |
                      +────|medical_test|──+
                           +-----------+
                           | test_id   |
                           | patient_id|
                           |treatment_id|
                           | test_name |
                           | test_date |
                           | result    |
                           | status    |
                           +-----------+
```

## Key Design Insights
- **`treatment` uses a surrogate PK** (`treatment_id`) instead of composite (`doctor_id + patient_id`), because the same doctor can treat the same patient multiple times on different dates. This differs from a pure junction table pattern.
- **Linking `medical_test` to both `patient_id` and `treatment_id`** — the `treatment_id` is nullable because some tests (like annual checkups) may not be tied to a specific treatment. Index on `(patient_id, test_date)` for quick patient history lookups.
- **Trade-off**: Healthcare data often requires audit trails. In production, you'd add `created_by`, `updated_at`, and consider soft deletes (`is_deleted`) since medical records should never be physically deleted for compliance reasons.
