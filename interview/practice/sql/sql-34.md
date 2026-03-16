**34.** Given the following table:
```sql
TABLE employee
    id INTEGER NOT NULL PRIMARY KEY
    managerId INTEGER REFERENCES employee(id)
    name VARCHAR(255) NOT NULL
```
Write a query to list all employees with their manager's name.

```SQL
select e.name as emp_name, m.name as man_name from employee e
left join employee m on m.id = e.managerId;
```
