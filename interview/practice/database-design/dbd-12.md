**12.** Design a database for a company's department structure. A department can have multiple employees. An employee belongs to one department. A department can have multiple projects. Describe the tables.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### department
| Column | Type | Constraints |
|--------|------|-------------|
| dept_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL, UNIQUE |
| location | VARCHAR(200) | NULL |
| budget | DECIMAL(15,2) | NULL |

### employee
| Column | Type | Constraints |
|--------|------|-------------|
| employee_id | BIGINT | PK, AUTO_INCREMENT |
| dept_id | BIGINT | FK → department(dept_id), NOT NULL |
| name | VARCHAR(200) | NOT NULL |
| email | VARCHAR(255) | UNIQUE, NOT NULL |
| position | VARCHAR(100) | NOT NULL |
| salary | DECIMAL(12,2) | NOT NULL |
| hire_date | DATE | NOT NULL |
| manager_id | BIGINT | FK → employee(employee_id), NULL |

### project
| Column | Type | Constraints |
|--------|------|-------------|
| project_id | BIGINT | PK, AUTO_INCREMENT |
| dept_id | BIGINT | FK → department(dept_id), NOT NULL |
| name | VARCHAR(200) | NOT NULL |
| start_date | DATE | NOT NULL |
| end_date | DATE | NULL |
| status | VARCHAR(20) | NOT NULL, DEFAULT 'ACTIVE' |
| budget | DECIMAL(12,2) | NULL |

### project_assignment (optional — Employee ↔ Project)
| Column | Type | Constraints |
|--------|------|-------------|
| employee_id | BIGINT | PK, FK → employee(employee_id) |
| project_id | BIGINT | PK, FK → project(project_id) |
| role | VARCHAR(100) | NOT NULL |
| assigned_at | DATE | NOT NULL |

## Relationships
- **Department → Employee**: 1:N (each employee belongs to one department)
- **Department → Project**: 1:N (each project belongs to one department)
- **Employee → Employee**: self-referential 1:N (manager hierarchy)
- **Employee ↔ Project**: M:N via `project_assignment` (optional but practical)

## ER Diagram
```
+-----------+       +------------+
| department|       |  employee  |
+-----------+       +------------+
| dept_id   |──1:N──| employee_id|
| name      |       | dept_id    |
| location  |       | name       |
| budget    |       | email      |
+-----------+       | position   |
     |              | salary     |
     |1:N           | hire_date  |
     |              | manager_id |──N:1──employee (self)
+-----------+       +------+-----+
|  project  |              |
+-----------+         M:N  |
| project_id|              |
| dept_id   |    +---+-----+-------+
| name      |    |project_assignment|
| start_date|    +------------------+
| end_date  |    |employee_id (PK)  |
| status    |    |project_id  (PK)  |
+-----------+    | role             |
                 | assigned_at      |
                 +------------------+
```

## Key Design Insights
- **Pure 1:N design** — department owns both employees and projects. The self-referential `manager_id` on employee is a bonus that models reporting hierarchy without an extra table. `WHERE manager_id IS NULL` finds top-level executives.
- **`project_assignment`** goes beyond the question's requirements but demonstrates real-world thinking — employees from the same department (or cross-department) work on projects. The `role` field captures their function on that specific project.
- **Trade-off**: Employee belongs to exactly one department (`dept_id` FK). In reality, matrix organizations have employees in multiple departments. If that's needed, you'd replace the FK with a junction table `employee_department`. Start simple (1:N) and only add complexity when the business requires it — interviewers appreciate this pragmatic evolution thinking.
