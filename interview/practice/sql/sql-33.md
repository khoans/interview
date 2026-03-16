**33.** Given the following tables:
```sql
TABLE departments
    id INTEGER NOT NULL PRIMARY KEY
    name VARCHAR(255) NOT NULL

TABLE employees
    id INTEGER NOT NULL PRIMARY KEY
    name VARCHAR(255) NOT NULL
    salary DECIMAL(10, 2)
    department_id INTEGER REFERENCES departments(id)
```
Write a query to find departments where the average salary is above the company-wide average salary.
