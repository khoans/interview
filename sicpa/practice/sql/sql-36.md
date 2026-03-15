**36.** Given the following table:
```sql
TABLE employee
    id INTEGER NOT NULL PRIMARY KEY
    managerId INTEGER REFERENCES employee(id)
    name VARCHAR(255) NOT NULL
    salary DECIMAL(10, 2)
```
Write a query to find employees who earn more than their managers.
