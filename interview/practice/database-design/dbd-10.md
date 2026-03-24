**10.** Design a database for a school. A teacher can teach multiple subjects. A student can take multiple subjects. A class has multiple students and one teacher. Describe the tables.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### teacher
| Column | Type | Constraints |
|--------|------|-------------|
| teacher_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| email | VARCHAR(255) | UNIQUE, NOT NULL |
| phone | VARCHAR(20) | NULL |

### subject
| Column | Type | Constraints |
|--------|------|-------------|
| subject_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| description | TEXT | NULL |

### teacher_subject (junction — Teacher ↔ Subject)
| Column | Type | Constraints |
|--------|------|-------------|
| teacher_id | BIGINT | PK, FK → teacher(teacher_id) |
| subject_id | BIGINT | PK, FK → subject(subject_id) |

### student
| Column | Type | Constraints |
|--------|------|-------------|
| student_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| email | VARCHAR(255) | UNIQUE, NOT NULL |
| dob | DATE | NOT NULL |
| grade_level | INT | NOT NULL |

### class
| Column | Type | Constraints |
|--------|------|-------------|
| class_id | BIGINT | PK, AUTO_INCREMENT |
| class_name | VARCHAR(100) | NOT NULL |
| teacher_id | BIGINT | FK → teacher(teacher_id), NOT NULL |
| subject_id | BIGINT | FK → subject(subject_id), NOT NULL |
| academic_year | VARCHAR(10) | NOT NULL |
| schedule | VARCHAR(200) | NULL |

### class_student (junction — Class ↔ Student)
| Column | Type | Constraints |
|--------|------|-------------|
| class_id | BIGINT | PK, FK → class(class_id) |
| student_id | BIGINT | PK, FK → student(student_id) |
| grade | VARCHAR(5) | NULL |

## Relationships
- **Teacher ↔ Subject**: M:N via `teacher_subject`
- **Teacher → Class**: 1:N (each class has one teacher)
- **Subject → Class**: 1:N (each class teaches one subject)
- **Class ↔ Student**: M:N via `class_student`

## ER Diagram
```
+---------+     +-----------------+     +---------+
| teacher |     | teacher_subject |     | subject |
+---------+     +-----------------+     +---------+
|teacher_id|─1:N|teacher_id (PK)  |N:1─|subject_id|
| name    |     |subject_id (PK)  |     | name    |
| email   |     +-----------------+     +----+----+
| phone   |                                  |
+----+----+                             1:N  |
     |1:N                                    |
     |          +---------+                  |
     +──────────| class   |──────────────────+
                +---------+
                | class_id|
                |teacher_id|
                |subject_id|
                |class_name|
                |acad_year |
                +----+----+
                     |1:N
              +------+--------+
              | class_student |
              +---------------+
              |class_id  (PK) |
              |student_id(PK) |──N:1──student
              | grade         |
              +---------------+
```

## Key Design Insights
- **Three separate relationships** connect these entities: teacher↔subject (who *can* teach what), teacher→class (who *does* teach this class), and class↔student (who *attends*). The first is a capability mapping; the latter two are actual assignments. Interviewers want to see you distinguish between capability and assignment.
- **`class` bridges teacher and subject** — it represents a specific teaching assignment for an academic year. The `teacher_subject` table captures qualifications; the `class` table captures the actual schedule.
- **Trade-off**: `grade` belongs on `class_student`, not on `student`, because a student has different grades per class. This is the classic "attribute of the relationship" pattern. A common pitfall is putting grades on the student table and losing per-class granularity.
