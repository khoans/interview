**17.** Design a database for a university research system. A professor can lead multiple research projects. A project can have multiple researchers. A researcher can work on multiple projects. Describe the tables.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### professor
| Column | Type | Constraints |
|--------|------|-------------|
| professor_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| email | VARCHAR(255) | UNIQUE, NOT NULL |
| department | VARCHAR(100) | NOT NULL |
| title | VARCHAR(50) | NOT NULL |

### researcher
| Column | Type | Constraints |
|--------|------|-------------|
| researcher_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| email | VARCHAR(255) | UNIQUE, NOT NULL |
| degree | VARCHAR(50) | NULL |
| specialization | VARCHAR(100) | NULL |

### project
| Column | Type | Constraints |
|--------|------|-------------|
| project_id | BIGINT | PK, AUTO_INCREMENT |
| professor_id | BIGINT | FK → professor(professor_id), NOT NULL |
| title | VARCHAR(500) | NOT NULL |
| description | TEXT | NULL |
| start_date | DATE | NOT NULL |
| end_date | DATE | NULL |
| funding_amount | DECIMAL(12,2) | NULL |
| status | VARCHAR(20) | NOT NULL, DEFAULT 'ACTIVE' |

### project_researcher (junction — Project ↔ Researcher)
| Column | Type | Constraints |
|--------|------|-------------|
| project_id | BIGINT | PK, FK → project(project_id) |
| researcher_id | BIGINT | PK, FK → researcher(researcher_id) |
| role | VARCHAR(100) | NOT NULL, DEFAULT 'MEMBER' |
| joined_date | DATE | NOT NULL |

## Relationships
- **Professor → Project**: 1:N (a professor leads many projects)
- **Project ↔ Researcher**: M:N via `project_researcher`

## ER Diagram
```
+-----------+       +-----------+       +--------------------+       +------------+
| professor |       |  project  |       | project_researcher |       | researcher |
+-----------+       +-----------+       +--------------------+       +------------+
|professor_id|─1:N─| project_id|──1:N──| project_id    (PK) |──N:1──|researcher_id|
| name      |      |professor_id|      | researcher_id (PK) |       | name       |
| email     |      | title     |       | role               |       | email      |
| department|      |description|       | joined_date        |       | degree     |
| title     |      | start_date|       +--------------------+       |specialization|
+-----------+      | end_date  |                                    +------------+
                   |funding_amt|
                   | status    |
                   +-----------+
```

## Key Design Insights
- **Professor leads, researchers participate** — this is a key modeling decision. The professor→project is 1:N (one lead), while researcher↔project is M:N (many collaborators). If a professor could also be a researcher on someone else's project, you'd either make professor inherit from researcher (shared person table) or add professors into the researcher table too.
- **`role` in the junction table** captures whether someone is a "Lead Researcher", "Data Analyst", "PhD Student", etc. This is valuable metadata that only makes sense in the context of the relationship, not on either entity alone.
- **Trade-off**: Keeping `professor` and `researcher` as separate tables assumes they're distinct roles. In reality, professors often participate as researchers on each other's projects. A cleaner production design would use a single `person` table with role-based junction tables. But for the interview question as stated, separate tables are the direct correct answer — then mention the unification as an optimization.
