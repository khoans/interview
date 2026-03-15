**38.** Given the following table:
```sql
TABLE employees
    id INTEGER NOT NULL PRIMARY KEY
    name VARCHAR(255) NOT NULL
    salary DECIMAL(10, 2)
```
Write a query to find all employees who have the same salary as at least one other employee.

```SQL
select * from employees
where salary in (select salary from employees group by salary having count(*) > 1)
```
