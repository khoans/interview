**38.** Job Portal: Companies, Job Listings, Candidates, Skills. A company can post multiple job listings. A candidate can apply to multiple jobs. A job can have multiple required skills.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### companies
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| industry | VARCHAR(100) | |
| website | VARCHAR(500) | |
| location | VARCHAR(200) | |

### job_listings
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| company_id | BIGINT | FK → companies(id), NOT NULL |
| title | VARCHAR(200) | NOT NULL |
| description | TEXT | NOT NULL |
| salary_min | DECIMAL(12,2) | |
| salary_max | DECIMAL(12,2) | |
| location | VARCHAR(200) | |
| job_type | VARCHAR(20) | NOT NULL (FULL_TIME, PART_TIME, CONTRACT) |
| status | VARCHAR(20) | NOT NULL DEFAULT 'OPEN' |
| posted_at | TIMESTAMP | NOT NULL DEFAULT NOW() |
| expires_at | DATE | |

### candidates
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(100) | NOT NULL |
| email | VARCHAR(255) | NOT NULL, UNIQUE |
| phone | VARCHAR(20) | |
| resume_url | VARCHAR(500) | |
| experience_years | INT | |

### skills
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(100) | NOT NULL, UNIQUE |

### applications (M:N — candidates ↔ job_listings)
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| candidate_id | BIGINT | FK → candidates(id), NOT NULL |
| job_id | BIGINT | FK → job_listings(id), NOT NULL |
| cover_letter | TEXT | |
| status | VARCHAR(20) | NOT NULL DEFAULT 'SUBMITTED' |
| applied_at | TIMESTAMP | NOT NULL DEFAULT NOW() |
| UNIQUE(candidate_id, job_id) | | |

### job_skills (M:N — job_listings ↔ skills)
| Column | Type | Constraints |
|--------|------|-------------|
| job_id | BIGINT | FK → job_listings(id), NOT NULL |
| skill_id | BIGINT | FK → skills(id), NOT NULL |
| is_required | BOOLEAN | NOT NULL DEFAULT TRUE |
| PK(job_id, skill_id) | | |

### candidate_skills (M:N — candidates ↔ skills)
| Column | Type | Constraints |
|--------|------|-------------|
| candidate_id | BIGINT | FK → candidates(id), NOT NULL |
| skill_id | BIGINT | FK → skills(id), NOT NULL |
| years_experience | INT | |
| PK(candidate_id, skill_id) | | |

## Relationships
- companies → job_listings: **1:N**
- candidates ↔ job_listings: **M:N** via `applications`
- job_listings ↔ skills: **M:N** via `job_skills`
- candidates ↔ skills: **M:N** via `candidate_skills`

## ER Diagram (Text)
```
[companies] 1───N [job_listings] 1───M [job_skills] M───1 [skills] 1───M [candidate_skills] M───1 [candidates]
                        │                                                                             │
                        └──────────────── M:N via [applications] ─────────────────────────────────────┘
```

## Example & Insight
The "skill match" query is the killer feature: `SELECT j.*, COUNT(cs.skill_id) as matched FROM job_listings j JOIN job_skills js ON j.id = js.job_id JOIN candidate_skills cs ON js.skill_id = cs.skill_id WHERE cs.candidate_id = :candidateId GROUP BY j.id ORDER BY matched DESC`. The `is_required` flag on `job_skills` lets you differentiate "must have Java" from "nice to have Kubernetes."

## Trade-off
Application status tracking (SUBMITTED → SCREENING → INTERVIEW → OFFER → REJECTED) could be a single column or a separate `application_events` table for full history. Single column is simpler; event history lets recruiters see the full timeline but adds complexity. Start with the column, add history table if audit requirements emerge.
