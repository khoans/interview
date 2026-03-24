**1.** Let's say we are creating a database for a university. We capture details about students who attend classes. A student can be enrolled in multiple classes at a time. A class can have many students. Describe the tables to be created.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### student
| Column | Type | Constraints |
|--------|------|-------------|
| student_id | BIGINT | PK, AUTO_INCREMENT |
| first_name | VARCHAR(100) | NOT NULL |
| last_name | VARCHAR(100) | NOT NULL |
| email | VARCHAR(255) | NOT NULL, UNIQUE |
| enrollment_date | DATE | NOT NULL |

### class
| Column | Type | Constraints |
|--------|------|-------------|
| class_id | BIGINT | PK, AUTO_INCREMENT |
| class_name | VARCHAR(200) | NOT NULL |
| instructor | VARCHAR(200) | NOT NULL |
| semester | VARCHAR(20) | NOT NULL |
| max_capacity | INT | NOT NULL, DEFAULT 30 |

### student_class (junction table)
| Column | Type | Constraints |
|--------|------|-------------|
| student_id | BIGINT | PK, FK → student(student_id) |
| class_id | BIGINT | PK, FK → class(class_id) |
| enrolled_at | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP |
| grade | VARCHAR(2) | NULL |

## Relationships
- **Student ↔ Class**: M:N resolved via `student_class` junction table
- student (1) → (N) student_class
- class (1) → (N) student_class

## ER Diagram
```
+-----------+       +----------------+       +---------+
|  student  |       |  student_class |       |  class  |
+-----------+       +----------------+       +---------+
| student_id|──1:N──| student_id (PK)|──N:1──|class_id |
| first_name|       | class_id   (PK)|       |class_name|
| last_name |       | enrolled_at    |       |instructor|
| email     |       | grade          |       |semester  |
| enroll_dt |       +----------------+       |max_cap   |
+-----------+                                +---------+
```

## Key Design Insights
- **Composite PK on junction table** (`student_id + class_id`) prevents duplicate enrollments. This is the classic M:N resolution pattern — every M:N relationship needs a junction table in relational databases.
- **Index on `class_id`** in junction table is critical — the composite PK already covers lookups by `student_id`, but queries like "find all students in class X" need a separate index on `class_id`.
- **Trade-off**: Storing `grade` in the junction table (not in student or class) is correct because it belongs to the *relationship*, not the entity. A common mistake is putting relationship attributes in the wrong table.
