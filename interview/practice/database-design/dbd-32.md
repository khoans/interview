**32.** LMS: Courses, Modules, Lessons, Students, Progress Tracking. A course can have multiple modules. A module can have multiple lessons. A student can enroll in multiple courses. Track student progress.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### students
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(100) | NOT NULL |
| email | VARCHAR(255) | NOT NULL, UNIQUE |
| registered_at | TIMESTAMP | NOT NULL DEFAULT NOW() |

### courses
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| title | VARCHAR(200) | NOT NULL |
| description | TEXT | |
| instructor | VARCHAR(100) | |
| total_lessons | INT | NOT NULL DEFAULT 0 |
| created_at | TIMESTAMP | NOT NULL DEFAULT NOW() |

### modules
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| course_id | BIGINT | FK → courses(id), NOT NULL |
| title | VARCHAR(200) | NOT NULL |
| sort_order | INT | NOT NULL |
| UNIQUE(course_id, sort_order) | | |

### lessons
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| module_id | BIGINT | FK → modules(id), NOT NULL |
| title | VARCHAR(200) | NOT NULL |
| content_url | VARCHAR(500) | |
| duration_min | INT | |
| sort_order | INT | NOT NULL |
| UNIQUE(module_id, sort_order) | | |

### enrollments (M:N — students ↔ courses)
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| student_id | BIGINT | FK → students(id), NOT NULL |
| course_id | BIGINT | FK → courses(id), NOT NULL |
| enrolled_at | TIMESTAMP | NOT NULL DEFAULT NOW() |
| completed_at | TIMESTAMP | |
| UNIQUE(student_id, course_id) | | |

### lesson_progress
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| student_id | BIGINT | FK → students(id), NOT NULL |
| lesson_id | BIGINT | FK → lessons(id), NOT NULL |
| status | VARCHAR(20) | NOT NULL DEFAULT 'NOT_STARTED' |
| started_at | TIMESTAMP | |
| completed_at | TIMESTAMP | |
| UNIQUE(student_id, lesson_id) | | |

## Relationships
- courses → modules: **1:N**
- modules → lessons: **1:N**
- students ↔ courses: **M:N** via `enrollments`
- students ↔ lessons: **M:N** via `lesson_progress`

## ER Diagram (Text)
```
[students] 1───M [enrollments] M───1 [courses] 1───N [modules] 1───N [lessons]
     │                                                                   │
     └─────────────────── M:N via [lesson_progress] ─────────────────────┘
```

## Example & Insight
Progress percentage = `COUNT(completed lessons) / total_lessons * 100`. Denormalize `total_lessons` on the course to avoid a 3-table join for every progress bar render. Update it via trigger or application logic when lessons are added/removed. Index `(student_id, status)` on `lesson_progress` for dashboard queries.

## Trade-off
Tracking progress at lesson-level gives fine granularity but creates many rows (1000 students x 50 lessons = 50K rows per course). For very large LMS platforms, consider tracking at module-level and only expanding to lesson-level when the student opens that module.
