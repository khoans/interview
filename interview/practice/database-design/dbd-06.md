**6.** Design a database for a gym membership system. A member can enroll in multiple classes. A trainer can conduct multiple classes. A class can have multiple members. Describe the tables.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### member
| Column | Type | Constraints |
|--------|------|-------------|
| member_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| email | VARCHAR(255) | UNIQUE, NOT NULL |
| phone | VARCHAR(20) | NULL |
| membership_type | VARCHAR(50) | NOT NULL |
| join_date | DATE | NOT NULL |
| expiry_date | DATE | NOT NULL |

### trainer
| Column | Type | Constraints |
|--------|------|-------------|
| trainer_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| specialization | VARCHAR(100) | NOT NULL |
| phone | VARCHAR(20) | NULL |

### gym_class
| Column | Type | Constraints |
|--------|------|-------------|
| class_id | BIGINT | PK, AUTO_INCREMENT |
| class_name | VARCHAR(200) | NOT NULL |
| trainer_id | BIGINT | FK → trainer(trainer_id), NOT NULL |
| schedule_day | VARCHAR(10) | NOT NULL |
| start_time | TIME | NOT NULL |
| duration_min | INT | NOT NULL |
| max_capacity | INT | NOT NULL |

### class_enrollment (junction — Member ↔ Class)
| Column | Type | Constraints |
|--------|------|-------------|
| member_id | BIGINT | PK, FK → member(member_id) |
| class_id | BIGINT | PK, FK → gym_class(class_id) |
| enrolled_at | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP |

## Relationships
- **Trainer → Gym Class**: 1:N (a trainer conducts many classes)
- **Member ↔ Gym Class**: M:N via `class_enrollment`

## ER Diagram
```
+---------+       +------------------+       +-----------+
| member  |       | class_enrollment |       | gym_class |
+---------+       +------------------+       +-----------+
|member_id|──1:N──| member_id (PK)   |──N:1──| class_id  |
| name    |       | class_id  (PK)   |       | class_name|
| email   |       | enrolled_at      |       | trainer_id|──N:1──trainer
| phone   |       +------------------+       |schedule_day|
|mem_type |                                  | start_time|
|join_date|                                  |duration_min|
|expiry_dt|                                  |max_capacity|
+---------+                                  +-----------+
```

## Key Design Insights
- **Trainer → Class is 1:N, not M:N** in this design. Each class has one assigned trainer. If you needed substitute trainers or co-trainers, you'd need a junction table, but the simplest correct model starts with 1:N and evolves only when needed (YAGNI).
- **`max_capacity`** on `gym_class` lets you enforce enrollment limits. The application should check `COUNT(*) FROM class_enrollment WHERE class_id = ?` before inserting — or use a trigger. This prevents overbooking.
- **Trade-off**: `membership_type` as VARCHAR is quick to implement, but in production you'd likely extract a `membership_plan` table with pricing, features, and duration rules. Denormalizing here is fine for an interview answer — just acknowledge the evolution path.
