**27.** Skill Management: Employees, Skills, Projects. An employee can have multiple skills. A project can require multiple skills. A skill can be possessed by multiple employees.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### employees
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(100) | NOT NULL |
| email | VARCHAR(255) | NOT NULL, UNIQUE |
| department | VARCHAR(100) | |
| hire_date | DATE | NOT NULL |

### skills
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(100) | NOT NULL, UNIQUE |
| category | VARCHAR(50) | (e.g., LANGUAGE, FRAMEWORK, DATABASE) |

### projects
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| start_date | DATE | |
| end_date | DATE | |
| status | VARCHAR(20) | NOT NULL DEFAULT 'PLANNED' |

### employee_skills (M:N — employees ↔ skills)
| Column | Type | Constraints |
|--------|------|-------------|
| employee_id | BIGINT | FK → employees(id), NOT NULL |
| skill_id | BIGINT | FK → skills(id), NOT NULL |
| proficiency | VARCHAR(20) | (BEGINNER, INTERMEDIATE, EXPERT) |
| PK(employee_id, skill_id) | | |

### project_skills (M:N — projects ↔ skills)
| Column | Type | Constraints |
|--------|------|-------------|
| project_id | BIGINT | FK → projects(id), NOT NULL |
| skill_id | BIGINT | FK → skills(id), NOT NULL |
| required_level | VARCHAR(20) | (BEGINNER, INTERMEDIATE, EXPERT) |
| PK(project_id, skill_id) | | |

## Relationships
- employees ↔ skills: **M:N** via `employee_skills`
- projects ↔ skills: **M:N** via `project_skills`

## ER Diagram (Text)
```
[employees] 1───M [employee_skills] M───1 [skills] 1───M [project_skills] M───1 [projects]
```

## Example & Insight
This enables a powerful "skill-gap query": find employees who match a project's required skills. `SELECT e.* FROM employees e JOIN employee_skills es ON e.id = es.employee_id JOIN project_skills ps ON es.skill_id = ps.skill_id WHERE ps.project_id = ? AND es.proficiency >= ps.required_level`. Index on `(skill_id, proficiency)` in both junction tables.

## Trade-off
Proficiency as an enum is simple but rigid. In practice, skills evolve — you might need a `certified_date` or `years_experience` column. But YAGNI: start simple, add columns when real requirements emerge.
