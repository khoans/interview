**24.** Given the following tables:
```sql
TABLE departments
    id INTEGER NOT NULL PRIMARY KEY
    name VARCHAR(255) NOT NULL
    budget DECIMAL(12, 2)

TABLE employees
    id INTEGER NOT NULL PRIMARY KEY
    name VARCHAR(255) NOT NULL
    department_id INTEGER REFERENCES departments(id)
```
Write a query to find departments that have no employees.
