**42.** Historical Salary Tracking: Employee salary records with effective date tracking over time.



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
| current_salary | DECIMAL(12,2) | NOT NULL |

### salary_history
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| employee_id | BIGINT | FK → employees(id), NOT NULL |
| salary | DECIMAL(12,2) | NOT NULL |
| currency | CHAR(3) | NOT NULL DEFAULT 'USD' |
| effective_date | DATE | NOT NULL |
| end_date | DATE | (NULL = current salary) |
| change_reason | VARCHAR(100) | (PROMOTION, ANNUAL_REVIEW, ADJUSTMENT) |
| approved_by | VARCHAR(100) | |
| created_at | TIMESTAMP | NOT NULL DEFAULT NOW() |
| UNIQUE(employee_id, effective_date) | | |

## Relationships
- employees → salary_history: **1:N**

## ER Diagram (Text)
```
[employees] 1───N [salary_history]
```

## Key Queries

**Current salary:**
```sql
SELECT * FROM salary_history
WHERE employee_id = :id AND end_date IS NULL;
```

**Salary at a specific date:**
```sql
SELECT * FROM salary_history
WHERE employee_id = :id
  AND effective_date <= :targetDate
  AND (end_date IS NULL OR end_date > :targetDate);
```

**Salary growth over time:**
```sql
SELECT effective_date, salary,
       salary - LAG(salary) OVER (ORDER BY effective_date) AS increase
FROM salary_history
WHERE employee_id = :id
ORDER BY effective_date;
```

## Example & Insight
This is the **Slowly Changing Dimension Type 2 (SCD2)** pattern. When inserting a new salary: (1) UPDATE current row's `end_date` to today, (2) INSERT new row with `effective_date = today, end_date = NULL`. Do both in a transaction. The `current_salary` on `employees` is denormalized for fast access — always keep it in sync.

## Trade-off
`end_date = NULL` for current record is intuitive but makes range queries require `COALESCE(end_date, '9999-12-31')`. Alternative: always set `end_date` to a far-future date ('9999-12-31') — simplifies range queries (`WHERE :date BETWEEN effective_date AND end_date`) but is less semantically clear. Pick one convention and stick to it across all temporal tables.
