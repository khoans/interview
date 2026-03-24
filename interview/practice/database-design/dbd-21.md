**21.** Design a database for a course enrollment system. A student can enroll in multiple courses. A course can have multiple students. A course can have multiple prerequisites. Describe the tables.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### student
| Column | Type | Constraints |
|--------|------|-------------|
| student_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| email | VARCHAR(255) | UNIQUE, NOT NULL |
| major | VARCHAR(100) | NULL |
| enrollment_year | INT | NOT NULL |

### course
| Column | Type | Constraints |
|--------|------|-------------|
| course_id | BIGINT | PK, AUTO_INCREMENT |
| course_code | VARCHAR(20) | UNIQUE, NOT NULL |
| title | VARCHAR(300) | NOT NULL |
| credits | INT | NOT NULL |
| description | TEXT | NULL |

### enrollment (junction — Student ↔ Course)
| Column | Type | Constraints |
|--------|------|-------------|
| student_id | BIGINT | PK, FK → student(student_id) |
| course_id | BIGINT | PK, FK → course(course_id) |
| semester | VARCHAR(20) | NOT NULL |
| grade | VARCHAR(5) | NULL |
| enrolled_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP |

### course_prerequisite (self-referential junction — Course ↔ Course)
| Column | Type | Constraints |
|--------|------|-------------|
| course_id | BIGINT | PK, FK → course(course_id) |
| prerequisite_id | BIGINT | PK, FK → course(course_id) |
| CHECK | | (course_id != prerequisite_id) |

## Relationships
- **Student ↔ Course**: M:N via `enrollment`
- **Course ↔ Course**: M:N self-referential via `course_prerequisite`

## ER Diagram
```
+---------+       +------------+       +----------+
| student |       | enrollment |       |  course  |
+---------+       +------------+       +----------+
|student_id|─1:N──|student_id(PK)|─N:1─|course_id |
| name    |       |course_id (PK)|     |course_code|
| email   |       | semester    |      | title    |
| major   |       | grade       |      | credits  |
|enroll_yr|       | enrolled_at |      |description|
+---------+       +------------+       +-----+----+
                                             |
                                    M:N self-referential
                                             |
                                  +----------+----------+
                                  | course_prerequisite |
                                  +---------------------+
                                  | course_id      (PK) |──FK──course
                                  | prerequisite_id(PK) |──FK──course
                                  +---------------------+
```

## Key Design Insights
- **Self-referential M:N** on course is the distinctive pattern here. `course_prerequisite` has two FKs both pointing to the `course` table. This means "to take course A, you must have completed courses B and C." The CHECK constraint prevents a course from being its own prerequisite.
- **Prerequisite validation at enrollment time**: The application must verify `SELECT prerequisite_id FROM course_prerequisite WHERE course_id = :target` and check the student has a passing grade for each: `EXISTS (SELECT 1 FROM enrollment WHERE student_id = :sid AND course_id = :prereq AND grade IS NOT NULL AND grade != 'F')`. This is business logic better handled in the application layer than triggers.
- **Trade-off**: Prerequisites can form chains (A requires B, B requires C). This is a DAG (Directed Acyclic Graph) — you must prevent cycles (A requires B, B requires A). Cycle detection is hard in SQL alone. In production, validate with application logic or use a recursive CTE to detect cycles before inserting. Also, `semester` in enrollment prevents re-enrollment in the same course in the same semester while allowing retakes in different semesters.
